---
layout: post
title:  "Intermediate JS pitfalls (browser)"
date:   2018-02-07 15:01:05 +0100
categories: javascript
---
## Scope
<script async src="//jsfiddle.net/spygi/cyrv6wzL/14/embed/js,result/"></script>

+ 3 scopes: global, function (with var) or block (with let)
+ Inner functions take the scope of the outer ones. Variable resolution happens with the innermost function first.
