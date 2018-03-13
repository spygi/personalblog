Tech blog using Jekyll engine, published on Github pages.

## Writing
### Test locally
+ Write a post under \_posts following the naming convention
+ `bundle exec jekyll serve` (check incremental builds, autogeneration)
+ Navigate to localhost:4000/

### Production
+ Just push to the gh_pages branch
+ Navigate to https://spygi.github.io/tech-blog/ (or the CNAME)

## Setup
### Resources
+ The guide I followed: [Github pages for Jekyll](https://help.github.com/articles/using-jekyll-as-a-static-site-generator-with-github-pages/)
+ And vice versa: [Jekyll for Github pages](https://jekyllrb.com/docs/github-pages/)
+ [Metadata exposed from Github gem](https://help.github.com/articles/repository-metadata-on-github-pages/#available-repository-metadata)

### Configuration on Github (pages) side
+ Regarding repos/branches: You can use Pages once per user/organisation (special repository named "spygi.github.io", master branch)
or per project (/docs folder in master or special "gh-pages" branch).
This repo is using the latter method with the "gh-pages" branch. To avoid confusion I deleted the master branch (locally and remotely). For convenience I set the gh-pages as the default upstream to push to.

+ [Serving through Github pages](https://help.github.com/articles/configuring-a-publishing-source-for-github-pages/#default-source-settings-for-repositories-without-the-username-naming-scheme) ![Github pages setup](assets/readme/github-pages-setup.png "Github pages setup")

+ \_site is .gitignored (that's where Jekyll puts its output)

+ CNAME file for custom domain.

+ Optional: (for PRs or when navigating to the Web UI of Github)
![Default branch setting on Github](assets/readme/github-default-branch.png "Default branch setting on Github")

### Jekyll configuration
+ Added the github-pages gem in the Gemfile

+ Ideally I would like to host this under a spygi.me/tech url but this is not possible with DNS since [DNS works on another layer](https://stackoverflow.com/questions/9444055/using-dns-to-redirect-to-another-url-with-a-path). For that I tried various combinations of the baseurl parameters in \_config.yml in order to both run locally and still have CSS and other assets correctly linked in Github.io. At the end, I used a different subdomain with an APEX as explained in \_posts/2018-02-09-dns-primer-for-non-devops-people.markdown

### Credits
Initial inspiration by [Laurent Pellegrino's blog](http://www.pellegrino.link/) and [Marco's](http://mziccard.me/) though neither of the the 2 worked out of the box for me: Laurent's worked locally but when pushing on Github.io, CSS and other static assets where not linked correctly (tried various combinations of base_url and relative_url but then linking of the pages would not work) and Marco used the special user repo method which I want to keep free for other stuff.
