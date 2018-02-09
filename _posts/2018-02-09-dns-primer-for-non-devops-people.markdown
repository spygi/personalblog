---
layout: post
title:  "DNS primer"
date:   2018-02-09 20:01:05 +0100
comments: true
categories: devops
---
## Context
I recently got started with Github pages as a cheap and easy solution to deploy static pages and wanted to use a custom domain.
So I bought a domain from Eurodns and was trying to configure Github pages to play nicely with it. In the process I realised I had forgotten some of the DNS basics - so here they are, maybe they help someone else.

This post is about DNS in general with some specifics to Eurodns, Github pages and Heroku apps.

## Basics
DNS is the protocol used to translate from domain names to IPs. It is an application layer (remember the [TCP/IP model](https://en.wikipedia.org/wiki/Internet_protocol_suite))? Some terminology: DNS zone, DNS record, [FQDN/PQDN Fully/Partial Qualified Domain Names](https://en.wikipedia.org/wiki/Fully_qualified_domain_name), TTL(Time To Live).

### DNS records
There are many types of records, I will only focus on what I needed:
+ `A` records point from a domain name to an IPv4. This is not great because in case something changes in your network, you need to manually update IPs.
+ `AAAA` records is the same but points to an IPv6.
+ `CNAME` records point to another domain instead of an IP. The destination domain can be in the same DNS zone or not (if not you append a "." at the end). `CNAME` is not very useful for 2 reasons:  
  > ...it isn’t possible to create a CNAME record at the apex – or root – of a domain.[1]  

  And
  > It should only be used when there are no other records on that name.[2]

    Trying to tie 2 CNAMEs from the same domain in Eurodns gives a nice fat red error: "Round Robin is not allowed on CNAME"
+ `APEX` (aka `Apex alias` or `ANAME`) similar to CNAME but without those 2 restrictions.
+ `URL` redirects from a domain to a URL.

## My configuration
+ I have registered the apex domain `spygi.me`(without www). First thing is to create a `URL` record from the `www.` version of the domain to the non `www`(you can choose 301, 302 or redirect in a frame). Apparently this sets up an `A` record behind the scenes. ![URL record]({{ "/assets/dns/url.png" | absolute_url }})
+ On the Github repo itself you need a CNAME file (you can create this file yourself or go to repo settings->Github pages)![CNAME setting on Github pages setting]({{ "/assets/dns/github-pages-cname.png" | absolute_url }})
This accepts only FQDN (no path otherwise you will get a mail saying "The CNAME `foo/bar` is not properly formatted"!) and what it does is a 301 to the domain specified (which at this point points to nowhere).
+ To fix that you need a `CNAME` (or `APEX`) from the FQDN you used before to `USERNAME.github.io.` (notice the trailing dot) This way your users are able to access your app only on one URL - the custom one - which is better for the end-user I assume. I used a `CNAME`: ![CNAME]({{ "/assets/dns/cname.png" | absolute_url }})

+ For Heroku, you only do the last step meaning if you navigate to your `app-name.herokuapp.com` you still see your app (besides on your custom domain). I used an `APEX` since I want to use my root domain `spygi.me`: ![APEX]({{ "/assets/dns/apex.png" | absolute_url }})

### SSL
"Wildcards" have to do with SSL certificates (not with subdomains). Specifically they give you the chance to issue one certificate for all \*.yourdomain.com for an additional charge (at least for Eurodns).

## Troubleshooting
+ Can I use DNS to point to a specific path? [No](https://stackoverflow.com/questions/9444055/using-dns-to-redirect-to-another-url-with-a-path)
+ Check your configuration with `dig` ![Dig command]({{ "/assets/dns/dig results.png" | absolute_url }})
+ Latency in changes can be due to TTL (in case of existing records) or DNS propagation (in case of newly created records).

## Resources
[1]: https://www.eurodns.com/blog/dns-alias-caa-records-for-domain-name
[2]: https://support.dnsimple.com/articles/differences-between-a-cname-alias-url/
+ [Configuring Apex records on Eurodns](https://eurodns.desk.com/customer/en/portal/articles/2915871-how-do-i-configure-an-alias-apex-alias-record-?b_id=16601)
+ [Setting up a CNAME from Github](https://help.github.com/articles/setting-up-a-custom-subdomain/)

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
