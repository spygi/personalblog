---
layout: post
title:  "Github API"
date:   2018-04-19
comments: true
categories: front-end
---
For my [personal site](http://spygi.me) I wanted to fetch data from Github API. Some context: the site is built using TravisCI and hosted in Heroku. No backend.

## Github API
Github has rate limiting of 60 requests per hour for unauthenticated requests or 5000 for authenticated ones. I could get away with unauthenticated requests maybe and good caching (how many people are gonna visit my site anyway?) but at least while developing I hit that limit often.

### The simple (but insecure) way
1. Create a personal access token from the Github UI with the right scopes
2. Use it in the code such as
{% highlight javascript %}
$.get({
    url: 'https://api.github.com/repos/USERNAME/REPO',
    beforeSend: function (xhr) {
        xhr.setRequestHeader("Authorization", "token " + PERSONAL_ACCESS_TOKEN); // notice the lower case t in token
    }
})
{% endhighlight %}

This works locally (you can see the `X-RateLimit-Limit` response header set to 5000) but when I pushed to Github, Github detects that there is an active token in a public repo and revokes it! I tried obfuscating it in HTML (and accessing it in JS with `document.querySelector("meta[name='gh:token']").getAttribute('content');`) but Github detected it as well. And of course it's not secure since it's available on JS (you can restrict the scopes the token can "access" but still).
Not sure how this guy/gal gets away with it [here](https://github.com/lepture/github-cards/blob/master/index.html) since they have client-id and client-secret in the code. They write at the [footer](https://lab.lepture.com/github-cards/) that it's "Hosted on Github" but I doubt it: DNS doesn't seem to redirect anywhere (and also there is no CNAME file at the repo)
```
dig +noall +answer lab.lepture.com
lab.lepture.com.	142	IN	A	104.28.19.92
lab.lepture.com.	142	IN	A	104.28.18.92
```
Anywaaaay...

### The "right" way
According to [this](https://developer.github.com/assets/images/intro-to-apps-flow.png) I need a Github app that access some of the data as me. The instructions to authenticate against a Github app are [here](https://developer.github.com/apps/building-github-apps/authentication-options-for-github-apps/#authenticating-as-a-github-app).

1. Create a Github app on the web ui and install it for my user only (not organisation). I provided a dummy webhook URL.
2. Generate a private key and store it locally.
3. Generate JWT using the ruby script they provide in the instructions. The ISS is the ID of the app.
4. Put that JWT in an `Authorization: Bearer JWT` header ("Bearer" with capital B) header together with an `Accept` header to find the access_token URL.
5. POST to that URL to create a new access token.
6. Now use this token for authenticated calls.

## However...
> Installation access tokens [] expire after one hour.

The access token expires.

### Workarounds?
Tried using an expiration time that is more than 10 minutes away, got a JWT but when I tried to use it got `"message": "'Expiration time' claim ('exp') is too far in the future"`

I can see two workarounds:
1. at build time you generate many JWTs for the future with start-expirations times that differ 10 minutes and store them somewhere that JS has access to. Now whenever an access token expires you would use say the 6th JWT to create a new access token. Too complicated and also needs a backend.   
1. Or you use a client side JWT generation which means that you need to upload the private key to Github (probably Github will also detect that and revoke it?) so that JS has access to it. No security advantage, just makes it a bit harder for the attacker to generate the token.

Ooor you encrypt the private key and put it into travis.yml. On build Travis decrypts it and one of the 2 ways above are followed...ok, it's the same as above.

## Dead-end
It seems I either need a Backend to update JWT or go with unauthenticated and cached requests.

I did the second, with JQuery's `cache: true` option. This does a conditional GET based on the ETAG returned which according to the documentation
> Making a conditional request and receiving a 304 response does not count against your Rate Limit, so we encourage you to use it whenever possible. https://developer.github.com/v3/


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
