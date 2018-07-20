<!--
.. title: Deploying Octopress to existing GitHub Pages repo
.. slug: deploying-octopress-to-existing-github-pages-repo
.. date: 2014-01-05 22:33:33+01:00
.. tags: 
.. category: 
.. link: 
.. description: 
.. type: text
-->

Today I've decided to move my blog from [Tumblr](http://tumblr.com) to [Octopress](http://octopress.org/) and merge it with my homepage which is hosted on [GitHub Pages](http://pages.github.com/). I realized I'm a unix-based guy and feel more comfortable with plain text files, Git, possibility to work offline and a set of scripts that I have access to than with some cool but untouchable web service. Recent static site generators like Middleman and Git-deployed hosting was like a holy grail sent from heavens. I used then before on other sites (eg. [harmoneye.com](http://harmoneye.com)). During a night I managed to set up an Octopress site locally, migrate the content and prepare for deployment. But the actual act of deploying to an existing GitHub Pages repository turned up to be a problem worth several hours of solving. Here's the description of how to do it as I didn't find the solution on the web yet. Hope it helps somebody.

<!-- TEASER_END -->

Octopress deployment to GitHub Pages is nicely described in the [documentation](http://octopress.org/docs/deploying/github/). However, it expects the target repo to be empty. This was not the case.

### The situation
- I have an clean local installation of Octopress filled with content, configuration and some little customizations (style changes, some plugins added, etc.).
- Also I have an existing GitHub Pages repository (https://github.com/bzamecnik/zamecnik.me) that contains a single static page generated by Middleman and available at http://zamecnik.me/ via CNAME. It is a Project repository, not a User/Organization one. So the sources are in the `master` branch and the HTML files for publishing in the `gh-pages` branch.
- The deployment rake task `rake deploy` generates the output files into the `_deploy` directory, makes a new nested Git repo there and sets its `origin` remote to the provided GitHub repository URL.

I'd like to deploy the local Octopress to this existing repository. The manual and the scripts, on the other hand, expect that the repository be empty. When I run `rake deploy` it tries to pull from the remote repository and it fails on some files that must be merged.

```
## Pushing generated _deploy website
To git@github.com:bzamecnik/zamecnik.me.git
 ! [rejected]        gh-pages -> gh-pages (non-fast-forward)
error: failed to push some refs to 'git@github.com:bzamecnik/zamecnik.me.git'
hint: Updates were rejected because the tip of your current branch is behind
hint: its remote counterpart. Integrate the remote changes (e.g.
hint: 'git pull ...') before pushing again.
hint: See the 'Note about fast-forwards' in 'git push --help' for details.
```

I didn't want to delete the repository and create a new one, since I wanted a smooth transition.

### The solution that worked for me

The rough plan was to manually merge both branches `master` and `gh-pages` and then let the deploy scripts do their usual work.

I ran the `rake setup_github_pages`, `rake generate` and `rake deploy` commands. And the last one failed that it is not able to pull from the `gh-pages` branch.

- So first I commited all my changes in the local Octopress `master` branch.
- Then with a clean staging area I pulled from `origin/master` to `master`. This filled the project with some Middleman files that coincidently were also located in the `source/` directory.
- In the Git client I reset and removed all those files to reverted back to the previous state.
- Then (being in the merge mode) I commited the merge commit (which had essentially no changes) but joined my local `master` branch with `origin/master`.
- Then I was able to push it without problems.

Then I had to switch to the nested repository located at the `_deploy` directory. It contained the local `gh-pages` branch an also `origin/gh-pages` which was in a different commit tree.

- I had to manually delete the local `gh-pages` branch (generated by `rake deploy`) with `git branch -D gh-pages`.
- Then I checked out the remote `origin/gh-pages` as a tracking branch with `git checkout -t  -b gh-pages origin/gh-pages`.
- Then I ran `rake deploy` again and it worked like charm by then since local commits were now just fast-forward compared to those in the origin.

And that's it!

Hope that it helps anyone.