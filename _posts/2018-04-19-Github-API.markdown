---
layout: post
title:  "Github API"
date:   2018-04-19
comments: true
categories: front-end
---
For my [personal site](https://spygi.me) I wanted to fetch data from Github API. Some context: the site is built using TravisCI and hosted in Heroku. No backend.

## Github API
Github has rate limiting of 60 requests per hour for unauthenticated requests or 5000 for authenticated ones. I could get away with unauthenticated requests maybe and good caching (how many people are gonna visit my site anyway?) but at least while developing I hit that limit often.

### The simple (but insecure) way
1. Create a personal access token from the Github UI with the right scopes
2. Use it in the code such as
{% highlight javascript %}
$.get({
    url: 'https://api.github.com/repos/USERNAME/REPO',
    beforeSend: function (xhr) {
        xhr.setRequestHeader("Authorization", "token " + PERSONAL_ACCESS_TOKEN)
    }
})
{% endhighlight %}

This works locally (you can see the `X-RateLimit-Limit` response header set to 5000) but when I pushed to Github, Github detects that there is an active token in a public repo and revokes it! I tried obfuscating it in HTML (and accessing it in JS with `document.querySelector("meta[name='gh:token']").getAttribute('content');`) but Github detected it as well. And of course it's not secure since it's available on JS (though I had set the scopes to a very restrictive subset).
Not sure how this guy/gal gets away with it [here](https://github.com/lepture/github-cards/blob/master/index.html) since they have client-id and client-secret in the code. They write at the [footer](https://lab.lepture.com/github-cards/) that it's "Hosted on Github" but DNS doesn't seem to redirect anywhere (and also there is no CNAME file at the repo)
```
dig +noall +answer lab.lepture.com
lab.lepture.com.	142	IN	A	104.28.19.92
lab.lepture.com.	142	IN	A	104.28.18.92
```

### The right way
According to [this](https://developer.github.com/assets/images/intro-to-apps-flow.png) I need a Github app that access some of the data as me. The instructions to authenticate against a Github app are [here](https://developer.github.com/apps/building-github-apps/authentication-options-for-github-apps/#authenticating-as-a-github-app).

1. Create a Github app and install it on my user. I provided a dummy webhook URL.
2. Generate private key and store it locally.
3. Generate JWT using the ruby script. The ISS is the ID of the app.
4. Put that JWT in the Authorization header and Accept to find the access_token URL
5. POST to create a new access token.
6. Now use this for authenticated calls.

The access token has a lifetime of an hour (?) after which I am not sure you can use the JWT again because it has a lifetime of 10 minutes.

{% if page.comments %}
<div id="disqus_thread"></div>
<script>
/**
*  RECOMMENDED CONFIGURATION VARIABLES: EDIT AND UNCOMMENT THE SECTION BELOW TO INSERT DYNAMIC VALUES FROM YOUR PLATFORM OR CMS.
*  LEARN WHY DEFINING THESE VARIABLES IS IMPORTANT: https://disqus.com/admin/universalcode/#configuration-variables*/
/*
var disqus_config = function () {
this.page.url = PAGE_URL;  // Replace PAGE_URL with your page's canonical URL variable
this.page.identifier = PAGE_IDENTIFIER; // Replace PAGE_IDENTIFIER with your page's unique identifier variable
};
*/
(function() { // DON'T EDIT BELOW THIS LINE
var d = document, s = d.createElement('script');
s.src = 'https://tech-spygi-me.disqus.com/embed.js';
s.setAttribute('data-timestamp', +new Date());
(d.head || d.body).appendChild(s);
})();
</script>
<noscript>Please enable JavaScript to view the <a href="https://disqus.com/?ref_noscript">comments powered by Disqus.</a></noscript>
<script id="dsq-count-scr" src="//tech-spygi-me.disqus.com/count.js" async></script>
{% endif %}
