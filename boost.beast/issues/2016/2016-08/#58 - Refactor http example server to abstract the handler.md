# #58 - Refactor http example server to abstract the handler [Closed]

> Username: vinniefalco  
> Created at: 2016-08-23 17:24:42 UTC  
> Updated at: 2017-06-21 01:00:10 UTC  
> Closed at: 2017-06-21 01:00:10 UTC  
> Url: https://github.com/boostorg/beast/issues/58  

The HTTP example servers (sync and async) should be refactored so that the handler for requests is supplied as a template argument. This would make it easier for people to re-use the server without having to modify the boilerplate.  
  
If the server is generic enough, we can consider moving it to `extras/` as a partly-official API.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2016-09-25 15:21:40 UTC  
> Url: https://github.com/boostorg/beast/issues/58#issuecomment-249427651  

There's some work on this here but it has design flaws:  
https://github.com/vinniefalco/Beast/commits/server

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-06-21 01:00:10 UTC  
> Url: https://github.com/boostorg/beast/issues/58#issuecomment-309932740  

This is done, part of server-framework.
