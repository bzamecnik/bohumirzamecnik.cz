<!--
.. title: Docker Compose in GitLab CI for integration tests
.. slug: gitlab-docker-compose-tests
.. date: 2018-08-17
.. tags: gitlab,docker,programming
.. category: programming
.. link:
.. description:
.. type: text
-->

How to run integrations tests in GitLab CI using nested Docker Compose and save artifacts? What looks trivial was a bit tricky. I wasted my time so you don't have to.

<!-- TEASER_END -->

## The story behind

At [Rossum](https://rossum.ai) we build our research and production code using GitLab CI. We use Docker to keep environments reproducible and easier to install. Recently I used Docker Compose to make an isolated dev environment for our prediction machine which consists of a Flask REST API, a worker, shared SQL database and S3 storage.

Soon after we had some glitch on production that passed manual testing and made some users unhappy. We quickly fixed that but realized we're afraid of deploying every release because we can break anything. The bug could have been easily found by integration tests. Yes, we need automated integration tests!

## Integration tests

The Docker Compose stack for development came handy and I wrote a suite of tests that run prediction on known documents, validate the results and check for differences to previous expected results.

I found [pytest-docker-compose](https://pypi.org/project/pytest-docker-compose/) plugin that runs Docker Compose stack as a fixture and provides it to individual unit tests. Very nice except that Python 2 is not supported anymore.

Instead I had to first run `docker-compose up`, then execute the tests in one of the containers, followed by `docker-compose down` in a shell script.

The docker-compose file for tests looks somehow like the following:

```yaml
version: '2.0'

services:
  tests:
    image: ${IMAGE}
    command: [
      # wait for the API for 5 seconds
      "tools/wait-for-it.sh", "api:80", "-t", "5", "--",
      # # -v (verbose), -s (show output), -x (stop at first failure)
      "pytest", "-vsx", "tests/integration/api_integration_test.py"
    ]
    environment:
      - API_URL=http://api
      - ARTIFACTS_DIR
    depends_on:
      - "api"
      - "worker"
  db:
    image: custom-db-image
    build:
      context: .
      dockerfile: Dockerfile.db.dev
  api:
    image: ${IMAGE}
    command: [
      "tools/wait-for-it.sh", "db:5432", "-t", "60", "--",
      "uwsgi", "--yaml", "config/uwsgi.yml",
    ]
    depends_on:
    - "db"
  worker:
    image: ${IMAGE}
    command: [
      "tools/wait-for-it.sh", "db:5432", "-t", "60", "--",
      "python", "worker.py"
    ]
    depends_on:
    - "db"
```

Note that we wait for the other services to be ready on given TCP port using the amazing [wait-for-it.sh](https://github.com/vishnubob/wait-for-it) script and then run the tests via `pytest`. The test basically sends a request to the API service via our [client library](https://pypi.org/project/rossum/) `rossum` (available on PyPI), then polls for the results using the `polling` package and then makes assertions on the JSON response.

The tests are inside a container for several reasons - common image containing our source code, python, pytest, dependecies, `wait-for-it.sh`, etc. (it could be better a separate small image); it can see the `api` service at `http://api:80` without binding to port on the host machine. Otherwise we'd have to select some random port to prevent conflicts.

> TIP: If you use AWS on GitLab CI we provide AWS credentials via env variables so we need to pass them though.

```yaml
some_job:
  environment:
  - AWS_ACCESS_KEY_ID
  - AWS_SECRET_ACCESS_KEY
```

On my machine it worked well, now it was time for GitLab CI automation.

## Docker in GitLab CI

We already use Docker executor in our GitLab runners. Typically in the workflow we build an image, run tests with that image and then either clean up or push the released images to our Docker repository. In this case we need to run `docker-compose` to start new containers and execute tests in one of them.

There are two options - Docker-in-Docker or using shared docker deamon. In the first case the GitLab CI job container provides it's own docker deamon and runs all the containers nested. In the latter case (that we use) all the containers are siblings and just share the same deamon (via mounted socket `/var/run/docker.sock`).

Using standard `docker:latest` image with `docker-compose`, `pip`, `bash` and `git` installed in addition we can run our Docker Compose stack easily.

```yaml
# .gitlab-ci.yml

stages:
  - build
  - test
  - cleanup

variables:
  IMAGE: foobar:${CI_COMMIT_SHA}

build-image:
  stage: build
  image: docker:latest
  script:
    - >
      docker build
      -f Dockerfile
      -t ${IMAGE}
      --pull .

test-api-integration:
  stage: test
  image: docker:latest
  before_script:
    - apk add --no-cache py-pip bash git
    - pip install docker-compose
  script:
    - tests/integration/test_api_integration.sh

cleanup:
  stage: cleanup
  when: always
  script:
    - docker rmi ${IMAGE} || true
```

The basic test runner would look like this:

```bash
#!/usr/bin/env bash

# tests/integration/test_api_integration.sh
# Print commands
set -x

# Make the container names unique
export COMPOSE_PROJECT_NAME=$(git rev-parse --short HEAD)

# the docker-compose file uses $IMAGE to set the project image
docker_compose_cmd="docker-compose -f docker-compose.api.test.yml"
${docker_compose_cmd} build
# Run service containers, except for test container
${docker_compose_cmd} up -d --no-deps db api worker

# Run the tests
${docker_compose_cmd} run --rm tests

# Clean up
${docker_compose_cmd} down
```

## Exit code

The above code runs the tests but always succeeds. The thing is that GitLab CI test jobs fail when the last command given non-zero exit code. It fails on first such command.

The `tests` container executes the python test file via `pytest` and exits with zero on success and non-zero on test failure. If we put the commands from the script directly to `.gilab-ci.yml` it would stop at the test command and never stop the dependent containers! The default in the bash script is to continue after a non-zero exit code. But the script itself returns the exit code of the last command.

What we want is to save the test exit code and return it at the end of the script.

```bash
# tests/integration/test_api_integration.sh
# [...]

# Run the tests
${docker_compose_cmd} run --rm tests

# keep the exit code from tests while we clean up the containers
exit_code=$?

# Clean up
${docker_compose_cmd} down

# return the original result of the test - for GitLab CI
exit ${exit_code}
```

Possibly we could just turn off failing on the first error `set +e` ([more info](https://www.davidpashley.com/articles/writing-robust-shell-scripts/)) in `.gitlab-ci.yml` but I find cleaner to have a separate script that can be ran separately from GitLab.

> TIP: In case of failure we can print the logs from the api and worker containers (before `docker-compose down`):

```bash
# report what happened wrong
if [ ${exit_code} -ne 0 ]; then
    ${docker_compose_cmd} logs api
    ${docker_compose_cmd} logs worker
fi
```

## Artifacts

Originally I wrote a pure regression test comparing current JSON outputs to previous ones, known to be good. It can report any mistake, but also fail on every slight change due to differently trained model. In the second iteration I just wrote a [JSON schema](http://json-schema.org/) for our API responses and validated againt it. Also the tests now make only a warning on non-empty JSON diff. For these tasks I recommend packages [jsonchema](https://github.com/Julian/jsonschema) and [jsondiff](https://github.com/ZoomerAnalytics/jsondiff).

Still, when we release a new feature and check the changes are as expected we want to fix the new results as expected. For that we'd like just to reuse what has been already computed (not to run any long computations again manually).

In other words we'd like our API test to produce and artifact - ideally a ZIP file containing a set of predicted JSONs that we can commit back to the repo (and as a bonus the JSON diffs to keep for reference).

GitLab CI allows jobs to store build artifact (such as compiled binaries or test results) so we can use this feature.

In `.gitlab-ci.yml` we can specify the path from where to copy the files, name of the resulting ZIP file, when to store (we want both after success an failure) and how long to keep them.

```yaml
# .gitlab-ci.yml

test-api-integration:
  stage: test
  image: docker:latest
  before_script:
    - apk add --no-cache py-pip bash
    - pip install docker-compose
  script:
    - tests/integration/test_api_integration.sh
  artifacts:
    name: "api_test_artifacts_${CI_COMMIT_REF_SLUG}_${CI_COMMIT_SHA}"
    paths:
      - build/api_tests/
    expire_in: 7 days
    when: always
```

Now to question is how to get the test output files to the GitLab job container.

For me it was a long painful trial-and-error journey to get this working. And the happiness when it finally started working was thus higher.

### What does not work

- The output files are in the `tests` container, not the `job` container, we need to transfer them.
- The source code is in an anonymous volume mounted in the `job` container, not at the host machine (actually it is but buried deep down in Docker `/var`).
- When mounting volume with source path in `job` container to the `tests` container the source path is at the host machine, not in the container! This is not Docker-in-Docker, they're siblings.
- We cannot add a new volume to the alredy running `job` container, except if we mount a global named volume to all containers (in GitLab's `config.toml`). Not our way.
- What worked when running a single container is `docker run --volumes-from $PARENT_CONTAINER_ID` ([idea from the docker fora](https://gitlab.com/gitlab-org/gitlab-runner/issues/1389)). This is in `docker-compose.yml` as [volumes_from](https://docs.docker.com/compose/compose-file/compose-file-v2/#volumes_from), but not optional. We need to run the tests also by hand, not nested in other container.

My friends:

- [Docker Compose docs](https://docs.docker.com/compose/compose-file/compose-file-v2/)
- [GitLab CI Artifacts docs](https://docs.gitlab.com/ce/user/project/pipelines/job_artifacts.html)
- `debugfs` - [given an inode find path on the filesystem](https://unix.stackexchange.com/questions/35292/quickly-find-which-files-belongs-to-a-specific-inode-number)
    - allows to track a file in container where is actually located on the host machine
    - `ls -lai some_file` -> inode
    - `sudo debugfs -R 'ncheck 1234567' /dev/sda2` - to find path for inode `1234567` on partition `/dev/sda2`

### What worked

After GitLab gave me some lectures of how containers in it actually work I found the only way is to mount existing volume with the source code from the "parent" `job` container to the "nested" `tests` container. We do it via an optional docker-compose file, since in case of running the tests locally we want to mount another volume for artifacts from local filesystem.

TIP: The trick to get id of current container [source](https://stackoverflow.com/questions/34496882/get-docker-container-id-from-container-name) is:

```
basename "$(cat /proc/1/cpuset)"| cut -c 1-12
```

Now the working way to mount the parent volume to the nested container:

```yaml
# .gitlab-ci.yml
test-api-integration:
  # ...
  before_script:
  - export ARTIFACTS_DIR="$(pwd)/build/api_tests/"
    # Causes to mount existing volume with source codes to store artifacts.
    # (We can't add another named volume to this container.)
    - export PARENT_CONTAINER_ID=$(basename "$(cat /proc/1/cpuset)"| cut -c 1-12)
  # ...
```

The optional docker-compose file with `volumes_from`. note the `container:` prefix - this is a different to plain `docker run`.

```yaml
version: '2.0'

services:
  tests:
    volumes_from:
      # Mounting volumes from parent container to nested one
      # so that we could store artifacts.
      # Unfortunately `docker-compose run` doesn't support `--volumes-from`
      # (https://github.com/docker/compose/issues/1769) and we want to have
      # it optional (for manual invocation of tests).
      - container:$PARENT_CONTAINER_ID
```

And the test runner script:

```bash
# tests_api_integration.sh

# - PARENT_CONTAINER_ID - (optional) id of parent container to mount its volumes
#   - this can be filled when running tests in nested container in GitLab CI
#   - and empty otherwise
# - ARTIFACTS_DIR - directory with artifacts at the host and tests container
#   - you can leave it to the default
#   - if PARENT_CONTAINER_ID is set, ARTIFACTS_DIR must be available at its mounted volumes

# [...]

docker_compose_cmd="docker-compose -f docker-compose.api.test.yml"

# Mount the volume with source codes from parent container where that artifacts should be stored to.
# For a nested container in GitLab CI the source volume path is relative in the docker daemon fs!
# If ARTIFACTS_DIR is not set, this will set it to default value.
mkdir -p ${ARTIFACTS_DIR:="$(pwd)/build/api_tests/"}

if [ ! -z ${PARENT_CONTAINER_ID} ]; then
    # for invocation in GitLab CI within a nested container
    docker_compose_cmd="$docker_compose_cmd -f docker-compose.api.test.gitlab.yml"
    MOUNT_ARTIFACT_VOLUME=""
else
    # for manual invocation
    MOUNT_ARTIFACT_VOLUME="-v ${ARTIFACTS_DIR}:${ARTIFACTS_DIR}"
fi

echo "MOUNT_ARTIFACT_VOLUME: ${MOUNT_ARTIFACT_VOLUME}"

# Run the tests and keep the exit code.
${docker_compose_cmd} run --rm ${MOUNT_ARTIFACT_VOLUME} tests

# [...]
```

Then the test stores the output files into the directory provided in `$ARTIFACTS_DIR` and the job container sees them.

The artifact files can be then listed in GitLab CI job page and downloaded together as a ZIP file.

## Manual invocation

A nice thing is we can also run the test suite manually outside of GitLab (eg. on some more powerful machine or a poor MacBook):

```bash
export IMAGE=foobar:$(git rev-parse --short HEAD)-test
docker build -f Dockerfile -t ${IMAGE} --pull .
tests/integration/test_api_integration.sh
# check the artifacts (extracted JSONs and differences to expected ones)
find build/api_tests/
```

## Conclusion

It took some time to set up but now we're able to run tests of integration of our REST API and woker via database, validate the resulting JSONs and compare with previous results. Now it might be good to optimize the tests.

Possibly if we took the time, build a separate image where tests can run directly in the job container we could save time of hacking how to obtain the artifacts.

&lt;ad&gt;Btw: If you need to reduce manual data entry work and automate extracting data from invoices check [Rossum](https://rossum.ai) and its new [Elis Data Extraction API](https://rossum.ai/developers) (our baby we're working hard to improve) that enables you to extract up to 300 invoices/month for free using some quite good machine learning.&lt;/ad&gt;

Stay tuned for more stories and tutorials from our research and development.
