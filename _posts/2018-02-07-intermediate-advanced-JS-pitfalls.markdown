---
layout: post
title:  "Intermediate to advanced JS pitfalls (browser)"
date:   2018-02-07 15:01:05 +0100
comments: true
categories: javascript
---
Some (advanced in my opinion) pitfalls while using JS in the browser. For the most part I have used ES5 (with the exception of `let`). Only dependency is JQuery (only to print the results).

I would suggest to try to guess the outcome in the console before taking a look. I find the hoisting part for example especially tricky and closures still get me from time to time.

## Scope
<script async src="//jsfiddle.net/spygi/cyrv6wzL/embed/js,result/"></script>

+ 3 scopes: global, function (with var) or block (with let)
+ Inner functions take the scope of the outer ones. Variable resolution happens with the innermost function first.

## Variable hoisting (and scope)
<script async src="//jsfiddle.net/spygi/ttg00b6w/embed/js,result/"></script>

## Reference system (and scope)
Same as Java's: call by value for simple values, objects references are passed by value which means if you change the whole reference you don't affect the original object but if you modify the reference you modify the original too.

Notice that in the following examples we pass the variables as arguments compared to the previous section.
<script async src="//jsfiddle.net/spygi/cv8zr0gm/embed/js,result/"></script>

## Callbacks and their context: closures
Note that the order the callbacks print stuff out is random because.. well they are callbacks.
<script async src="//jsfiddle.net/spygi/v6L5sLqo/embed/js,result/"></script>

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
