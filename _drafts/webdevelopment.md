
## Basics
[Cookies vs local/session storage](https://stackoverflow.com/a/19869560/2259743)
Some considerations:
- Size of data they can hold
- Lifetime
- Security (e.g. `HttpOnly` or `Secure` on Cookies)
- Load on application
- Ease of access for the front-end developer

[Cookies in depth from MDN](https://developer.mozilla.org/en-US/docs/Web/HTTP/Cookies)
- HttpOnly is only sent to the server (recommendation against XSS)
- Secure cookies are only returned on HTTPS connections (Chrome and FF block them on http)

### Privacy
+ [Do not track](http://donottrack.us/) is implemented as an HTTP header.
+ Users can opt out from interest-based advertising [here](http://optout.aboutads.info) (preference might be stored in a cookie)
+ 1st party cookies are the ones set by the site you are visiting, 2nd party are from a "trusted partner" (eg. sister companies), 3rd party come from an advertiser or other external provider.


## Latency
http://www.webperformancetoday.com/2010/07/09/waterfalls-101/


## Transport/Application layer
- TCP
  - handshake: Syn, syn-ack, ack
  - congestion: slow start, congestion window
- HTTP (usually over TCP). HTTP vs HTTPS: SSL handshake
- [HTTP2](https://http2-explained.haxx.se/content/en/part6.html): binary protocol (= faster)
  + Compression of HTTP Headers
  + Server push: sent additional resources to the client without the client asking (improve cache hits)
  + Multiple requests over a single TCP connection
  + Atm only SSL version from browsers.


## Security
[OWASP top 10 2017](https://www.owasp.org/images/7/72/OWASP_Top_10-2017_%28en%29.pdf.pdf)
- [Same origin policy](https://en.wikipedia.org/wiki/Same-origin_policy#Relaxing_the_same-origin_policy): scripts from the same origin can share data. Origin is defined as URI scheme, (exact) host name, and port number

![Same origin]({{ "/assets/web/same-origin" | absolute_url }})

  - Some [exceptions](https://en.wikipedia.org/wiki/Same-origin_policy#Relaxing_the_same-origin_policy):
    - CORS, Websockets
- XSS: a type of injection, in which malicious scripts are injected into trusted websites.
  - That can be in a script tag, HTML comment, attribute name, tag name or directly in CSS (see [rule 0](https://www.owasp.org/index.php/XSS_(Cross_Site_Scripting\)\_Prevention_Cheat_Sheet)
  - Prevention is all about escaping properly data served (not just HTML encoding), especially if they contain user generated content.
- [CSRF/XSRF](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)):
> an attack that forces a user to execute unwanted actions on a web application in which they're currently authenticated. CSRF attacks specifically target state-changing requests, not theft of data, since the attacker has no way to see the response to the forged request.

  - Can be specially-crafted image tags, hidden forms, and JavaScript XMLHttpRequests via email or chat. A common variant is forging login requests.
  - [Mitigation](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)\_Prevention_Cheat_Sheet):
    - Allowing specific safe headers
    - submitting a (server generated) token with every XMLHTTPrequest or/and a cookie

[Difference of the 2](https://en.wikipedia.org/wiki/Cross-site_request_forgery)
> Unlike cross-site scripting (XSS), which exploits the trust a user has for a particular site, CSRF exploits the trust that a site has in a user's browser.

[But](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)\_Prevention_Cheat_Sheet)
> XSS is not necessary for CSRF to work. However, any XSS can be used to defeat token, Double-Submit cookie, referer and origin based CSRF defenses.


### Crypto
- Symmetric: same key, block or stream ciphers e.g. hashes (md5, sha etc), aes/des
- Asymmetric (public key): RSA/DSA
  - Signature: the message comes from the person that claims to have sent it (authentication)
    - Sign with your private, they read with your public
  - Encryption (authorization)
    - Sign with their public, only they can read it with their private:


Feature detection: detect features on the browser in HTML, [CSS](https://developer.mozilla.org/en-US/docs/Learn/Tools_and_testing/Cross_browser_testing/Feature_detection#CSS) or in JS eval("let a") Modernizr https://developer.mozilla.org/en-US/docs/Learn/Tools_and_testing/Cross_browser_testing/Feature_detection#JavaScript

VS browser sniffing

Polyfills:



Can i use

[How browsers work](https://www.html5rocks.com/en/tutorials/internals/howbrowserswork/)

[DOM hacks](https://hacks.mozilla.org/2017/09/building-the-dom-faster-speculative-parsing-async-defer-and-preload/)
