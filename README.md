Tech blog using Jekyll engine, published on Github pages.

## Writing
### Test locally
+ Write a post under \_posts following the naming convention
+ `bundle exec jekyll serve` (check incremental builds, autogeneration)
+ Navigate to localhost:4000/tech-blog/ # baseurl in \_config.yml

### Production
+ Just push to gh_pages branch
+ Navigate to https://spygi.github.io/tech-blog/

## Setup
### Resources
+ The guide I followed: [Github pages for Jekyll](https://help.github.com/articles/using-jekyll-as-a-static-site-generator-with-github-pages/)
+ And vice versa: [Jekyll for Github pages](https://jekyllrb.com/docs/github-pages/)
+ [Metadata exposed from Github gem](https://help.github.com/articles/repository-metadata-on-github-pages/#available-repository-metadata)

### Github pages
+ Regarding repos/branches: You can use Pages once per user/organisation (special repository named "spygi.github.io", master branch)
or per project (/docs folder in master or special "gh-pages" branch).
This repo is using the last method with the "gh-pages" branch. To avoid confusion I deleted the master branch (locally and remotely). For convenience I set the gh-pages as the default upstream to push to.

+ [Serving through Github pages](https://help.github.com/articles/configuring-a-publishing-source-for-github-pages/#default-source-settings-for-repositories-without-the-username-naming-scheme) ![Github pages setup](assets/readme/github-pages-setup.png "Github pages setup")

+ \_site is .gitignored.

+ Optional for PRs/when navigating to the Web UI of Github:
![Default branch setting on Github](assets/readme/github-default-branch.png "Default branch setting on Github")

### Jekyll configuration
+ Added the github-pages gem

+ In order to run locally (and still have CSS and other assets correctly linked in Github.io) I had to add `baseurl: "/tech-blog"` in \_config.yml. I tried `url: site.github.url` which worked locally but not remotely (3c1f609a96d1393cd980b205179596ca840fd03d) and `baseurl: site.config.baseurl` which did not work locally (with or without repository: spygi/tech-blog 18034b819cb19b014a8249fcde52772627e3dc59).

### Credits
Initial inspiration by [Laurent Pellegrino's blog](http://www.pellegrino.link/) and [Marco's](http://mziccard.me/) though neither of the the 2 worked out of the box for me: Laurent's worked locally but when pushing on Github.io, CSS and other static assets where not linked correctly (tried various combinations of base_url and relative_url but then linking of the pages would not work) and Marco used the special user repo method which I want to keep free for other stuff.
