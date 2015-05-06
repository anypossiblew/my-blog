---
layout:     post
title:      "About Tokens"
subtitle:   "10 Things You Should Know about Tokens"
date:       2015-04-30 12:00:00
author:     "暗梅幽闻花"
header-img: "img/about-angular.jpg"
---
<p>Couple weeks ago we published a short article about cookies vs tokens in the context of single page applications, in particular applied to AngularJs apps. It seems the community is interested in this topic, so we published a second article on token based authentication in realtime frameworks like socket.io. There is a great interest in this subject so we decided to continue with an article that explores in more detail some of the most common questions around token-based authentication. So here we go...</p>

<ul>
<li><a href="#token_storage">1. Tokens need to be stored somewhere (local/session storage or cookies)</a></li>
<li><a href="#token_storage">2. Tokens can expire like cookies, but you have more control</a></li>
<li><a href="#token_storage">3. Local/session storage won't work across domains, use a marker cookie</a></li>
<li><a href="#token_storage">4. Preflight requests will be sent on each CORS request</a></li>
<li><a href="#token_storage">5. When you need to stream something, use the token to get a signed request</a></li>
<li><a href="#token_storage">6. It's easier to deal with XSS than XSRF</a></li>
<li><a href="#token_storage">7. The token gets sent on every request, watch out its size</a></li>
<li><a href="#token_storage">8. If you store confidential info, encrypt the token</a></li>
<li><a href="#token_storage">9. JSON Web Tokens can be used in OAuth</a></li>
<li><a href="#token_storage">10. Tokens are not silver bullets, think about your authorization use cases carefully</a></li>
</ul>


<h3 id="token_storage">1. Tokens need to be stored somewhere (local/session storage or cookies)</h3>
<p>In the context of tokens being used on single page applications, some people have brought up the issue about refreshing the browser, and what happens with the token. The answer is simple: you have to store the token somewhere: in session storage, local storage or a client side cookie. Most session storage polyfills fallback to cookies when the browser doesn't support it.</p>
<p>If you are wondering "but if I store the token in the cookie I'm back to square one". Not really, in this case you are using cookies as a storage mechanism, not as an authentication mechanism (i.e. the cookie won't be used by the web framework to authenticate a user, hence no XSRF attack)</p>

<h3>2. Tokens can expire like cookies, but you have more control</h3>
