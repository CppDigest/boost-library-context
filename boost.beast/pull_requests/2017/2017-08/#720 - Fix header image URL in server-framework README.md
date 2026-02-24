# #720 Fix header image URL in server-framework README [Closed]

> Username: agnat  
> Created at: 2017-08-06 14:37:06 UTC  
> Updated at: 2017-08-08 14:18:31 UTC  
> Closed at: 2017-08-08 14:12:52 UTC  
> Url: https://github.com/boostorg/beast/pull/720  

All said. Looking forward to trying out beast. Thanks and congrats, @vinniefalco.

---

## Comment 1

> Username: vinniefalco  
> Created_at: 2017-08-06 16:53:28 UTC  
> Url: https://github.com/boostorg/beast/pull/720#issuecomment-320518550  

I have a confession to make, that **server-framework** example is terribly over-engineered. It is slow to compile and simply does too much. I'm working on a top to bottom rewrite of all the examples to make them simple and self contained (each in just one file). I will preserve some of the cool individual features of the server-framework example: pipelining, SSL detection, WebSocket+HTTP on the same port, but it will be broken up across several examples instead of all piled into one.  
  
I will probably get rid of that "PortHandler" concept since its a poor implementation of HTTP server request-routing, I think it is doing more harm than good. I appreciate your effort at trying to tidy up the repository though! Thanks!

---

## Comment 2

> Username: agnat  
> Created_at: 2017-08-08 14:12:52 UTC  
> Url: https://github.com/boostorg/beast/pull/720#issuecomment-320968436  

Ok. Wow. Just let me browse the *new* examples...

---

## Comment 3

> Username: vinniefalco  
> Created_at: 2017-08-08 14:18:31 UTC  
> Url: https://github.com/boostorg/beast/pull/720#issuecomment-320970045  

**advanced-server** and **advanced-server-ssl** are the most similar to the old **server-framework**.  
  
I would love to hear what you think of these new examples, hopefully there is something there you can use!

---
