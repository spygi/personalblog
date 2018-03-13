---
layout: post
title:  "Circular progress stepper/bar"
date:   2018-02-21 15:01:05 +0100
comments: true
categories: front-end
---
I set out to create a circular progress bar using CSS for a site I am working on. This is what I learned in the process.

## My requirements:
1. I wanted to play with CSS gradients so.. it had to be colorful!
1. Colors should not be repeated in the bar.
1. The site I am working on requires 4 or so different states, so it's more like a "stepper" than a progress bar.
1. The progress bar should be animatable.

## Initial research
+ React circular progress bar [NPM package](https://www.npmjs.com/package/react-circular-progressbar) Functionality-wise pretty much what I wanted (except for the gradients) to do plus more but built with SVG and React. Maybe I could customize it but I felt more like doing something new.
+ Pure CSS radial progress bar [Codepen](https://codepen.io/jo-asakura/pen/stFHi?editors=1100): this one uses the same trick with rotating rectangulars mentioned in Lea Verou's [article](https://www.smashingmagazine.com/2015/07/designing-simple-pie-charts-with-css/) but for the border. Unfortunately it also uses the `clip` property which is deprecated.. Also, no gradient.


## Possible solutions
This is what "I" came up with with quite some help from the internets.
<p data-height="265" data-theme-id="0" data-slug-hash="yvKxYe" data-default-tab="css,result" data-user="spygi" data-embed-version="2" data-pen-title="Circular progress stepper" class="codepen">See the Pen <a href="https://codepen.io/spygi/pen/yvKxYe/">Circular progress stepper</a> by Spyros Gi (<a href="https://codepen.io/spygi">@spygi</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

1. Using 2 background linear gradients.
+ I started by using the `border-image: linear-gradient` with `border-radius` for rounded corners. However as these [don't work together] (https://stackoverflow.com/questions/5706963/possible-to-use-border-radius-together-with-a-border-image-which-has-a-gradient) I used the workaround in the [answers](https://stackoverflow.com/a/29223812/2259743) below of 2 circles with different radius, one covering the other. This technique is used in all 3 solutions either as an pseudoelement or a regular one.
+ Because I needed different colors on the bar, I used the method of 2 half circles [as shown here](https://codepen.io/xram/pen/thLsk) from [Steve Marx](https://codepen.io/xram).  

Limitations: the calculations get complicated for intermediate steps. Notice that the colors don't always stay in the same position for different percentages because of how gradient works. For example 75% vs 87.5%. Also because it's done with linear gradient the bar is cut of in a weird angle.. you can set another angle in the gradient but for some values it will always look weird. You can stack linear gradients too.

1. Using [stacked radial gradients](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Images/Using_CSS_gradients) (it's weird to get the colors you need)

1. After implementing my solution I came across Lea’s conic gradient polyfill (again mentioned by Sarah Soueidan on her excellent [CSS vs SVG roundup](https://theblog.adobe.com/css-vs-svg-the-final-roundup/)). Here you need a normal element because we are using a polyfill.

{% highlight scss %}
// written in a super-verbose way
@if $progress == nth($progress-steps, 9) {
  background: linear-gradient($background-color 0%, rgba(nth($progress-colors, 5), 1) 0, rgba(nth($progress-colors, 4), 1) 50%, rgba(nth($progress-colors, 3), 1) 100%);
} @else if $progress == nth($progress-steps, 8) {
  background: linear-gradient($background-color 25%, rgba(nth($progress-colors, 5), 0.5) 25%, rgba(nth($progress-colors, 4), 1) 50%, rgba(nth($progress-colors, 3), 1) 100%);
} @else if $progress == nth($progress-steps, 7) {
  background: linear-gradient($background-color 50%, rgba(nth($progress-colors, 5), 0) 50%, rgba(nth($progress-colors, 4), 1) 50%, rgba(nth($progress-colors, 3), 1) 100%);
} @else if $progress == nth($progress-steps, 6) {
  background: linear-gradient($background-color 75%, rgba(nth($progress-colors, 5), 0) 75%, rgba(nth($progress-colors, 4), 0.5) 75%, rgba(nth($progress-colors, 3), 1) 100%);
}
{% endhighlight %}

## What I learned
+ SVG is a better tool for that :)
+ Saas: `percentage` function,  [interpolation](http://sass-lang.com/documentation/file.SASS_REFERENCE.html#interpolation_) and "-" sign need a space [before and after](https://stackoverflow.com/questions/8001879/subtraction-not-working-in-compass-scss)
+ Codepen: you can print out [Sass output in console](https://blog.codepen.io/2016/02/15/sass-debug-and-warn-output-to-the-console/) and you can save a pen in a Gist <3
+ CSS: [Css transitions don't work with gradients](https://stackoverflow.com/questions/6542212/use-css3-transitions-with-gradient-backgrounds) (I used opacity instead)
+ Polyfill's and pseudoelements don't work together.

## Moar Resources
+ https://css-tricks.com/examples/GradientBorder/

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
