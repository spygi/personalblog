My tech blog built with Jekyll and published on Github pages. Uses the [basically basic](https://github.com/mmistakes/jekyll-theme-basically-basic) from [this awesome guy](https://twitter.com/mmistakes).

## Writing
### Locally
+ Posts go under \_posts (or \_drafts)
+ `bundle exec jekyll serve` (check incremental builds, autogeneration)
+ Navigate to localhost:4000/

### Publishing
+ Just push to the ~~gh_pages~~ master branch
+ Navigate to https://spygi.github.io/tech-blog/ (or the CNAME)

## Setting up
### Resources
+ The guide I followed: [Github pages for Jekyll](https://help.github.com/articles/using-jekyll-as-a-static-site-generator-with-github-pages/)
+ And vice versa: [Jekyll for Github pages](https://jekyllrb.com/docs/github-pages/)
+ [Metadata exposed from Github gem](https://help.github.com/articles/repository-metadata-on-github-pages/#available-repository-metadata)

### Configuration on Github (pages) side
+ Regarding repos/branches: You can use Github Pages once per user/organisation (special repository named "spygi.github.io", master branch) or per project (on master branch, master branch `/docs` folder or special "gh-pages" branch). This repo is using the per repo method with the `master` branch (not /docs)

+ [Serving through Github pages](https://help.github.com/articles/configuring-a-publishing-source-for-github-pages/#default-source-settings-for-repositories-without-the-username-naming-scheme) ![Github pages setup](assets/readme/github-pages-setup.png "Github pages setup")

+ \_site is .gitignored (that's where Jekyll puts its output)

+ CNAME file for custom domain.

### Jekyll configuration
+ Added the github-pages gem in the Gemfile

+ Ideally I would like to host this under a spygi.me/tech url but this is not possible with DNS since [DNS works on another layer](https://stackoverflow.com/questions/9444055/using-dns-to-redirect-to-another-url-with-a-path). For that I tried various combinations of the baseurl parameters in \_config.yml in order to both run locally and still have CSS and other assets correctly linked in Github.io. At the end, I used a different subdomain with an APEX as explained [here](http://tech.spygi.me/devops/dns-primer-for-non-devops-people/)

### Theme configuration
+ Regarding themes the easiest way to publish on Pages is by using one of the [Github pages supported themes](https://pages.github.com/themes/). You simply add them on the Gemfile and \_config.yml and you are done.
+ ~~I didn't find something that I liked so I added Travis integration following instructions from the [Jasper 2 theme](https://github.com/jekyller/jasper2). The idea is by pushing to master, a Travis job is started to build the site with Rake and then push it to the `gh-pages` branch. For more details see [the related post](http://tech.spygi.me/devops/Travis-CI/).~~ This is not required anymore since Github added full support of [any Github-located theme](https://blog.github.com/2017-11-29-use-any-theme-with-github-pages/). I just use `remote_theme: owner/name` in \_config.yml

## Credits
Initial inspiration by [Laurent Pellegrino's blog](http://www.pellegrino.link/) and [Marco's](http://mziccard.me/) though neither of the the 2 worked out of the box for me: Laurent's worked locally but when pushing on Github.io, CSS and other static assets where not linked correctly (tried various combinations of base_url and relative_url but then linking of the pages would not work) and Marco used the special user repo method which I want to keep free for other stuff.

Thanks to [Michael aka mmistakes](https://twitter.com/mmistakes) for the theme.
