# bohumirzamecnik.cz

https://bohumirzamecnik.cz/

https://github.com/bzamecnik/bohumirzamecnik.cz

It uses Nikola static website generator.

```
mkvirtualenv nikola
pip install nikola[Extras] pyyaml

# builds and serves a site; automatically detects site changes, rebuilds, and optionally refreshes a browser
nikola auto

# create a new page in the site
nikola new_page
# create a new blog post or site page
nikola new_post

# publish
nikola github_deploy
```
