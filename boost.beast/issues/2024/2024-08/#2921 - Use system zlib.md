# #2921 - Use system zlib? [Closed]

> Username: bgemmill  
> Created at: 2024-08-19 04:51:17 UTC  
> Updated at: 2024-08-19 22:08:24 UTC  
> Closed at: 2024-08-19 22:08:24 UTC  
> Url: https://github.com/boostorg/beast/issues/2921  

For those of us with a system zlib installed, is there a way to tell beast to use it rather than the internal header implementation?  
  
In my particular case, I'm using boost::iostreams and all ready have the adapters built for a number of compression libraries.  
  
### Version of Beast  
  
beast from boost 1.86  
  
### Steps necessary to reproduce the problem  
  
1) Connect to a websocket seerver somewhere and request compression.  
2) Receive data  
3) Observe that [this code](https://www.boost.org/doc/libs/1_86_0/boost/beast/zlib/zlib.hpp) is used  
  
### All relevant compiler information  
```  
$ clang --version  
Ubuntu clang version 18.1.8  
```

---

## Comment 1

> Username: ashtum  
> Created at: 2024-08-19 05:29:26 UTC  
> Url: https://github.com/boostorg/beast/issues/2921#issuecomment-2295699745  

If you want to use it for inflating/deflating HTTP messages, you are free to use any method you prefer, as there is no dependency on them in the implementation. However, `websocket::stream` is tightly coupled with `zlib::deflate_stream` and `zlib::inflate_stream`, so you cannot substitute your own implementation.

---

## Comment 2

> Username: bgemmill  
> Created at: 2024-08-19 07:03:15 UTC  
> Url: https://github.com/boostorg/beast/issues/2921#issuecomment-2295812940  

Thanks for the heads up, the websocket stream is the one I'm interested in.  
  
You're saying it's so tightly coupled that it's not reasonable to use a system implementation?

---

## Comment 3

> Username: ashtum  
> Created at: 2024-08-19 07:08:47 UTC  
> Url: https://github.com/boostorg/beast/issues/2921#issuecomment-2295822238  

> You're saying it's so tightly coupled that it's not reasonable to use a system implementation?  
  
I mean, the interface doesn't allow you to substitute your own implementation.

---

## Comment 4

> Username: bgemmill  
> Created at: 2024-08-19 07:12:48 UTC  
> Url: https://github.com/boostorg/beast/issues/2921#issuecomment-2295828519  

Could that be changed? That's largely why I filed this report :-)

---

## Comment 5

> Username: ashtum  
> Created at: 2024-08-19 07:36:34 UTC  
> Url: https://github.com/boostorg/beast/issues/2921#issuecomment-2295867676  

> Could that be changed? That's largely why I filed this report :-)  
  
Do you have a motivating use case for it?

---

## Comment 6

> Username: vinniefalco  
> Created at: 2024-08-19 14:30:12 UTC  
> Url: https://github.com/boostorg/beast/issues/2921#issuecomment-2296722803  

It can't be changed.

---

## Comment 7

> Username: bgemmill  
> Created at: 2024-08-19 21:53:37 UTC  
> Url: https://github.com/boostorg/beast/issues/2921#issuecomment-2297523506  

@ashtum The motivator behind filing this is to get bugfixes for CVEs like [this one](https://github.com/madler/zlib/issues/868) with OS updates.  
  
Is beast also tracking these fixes, or is it an entirely different implementation at this point?

---

## Comment 8

> Username: vinniefalco  
> Created at: 2024-08-19 22:06:07 UTC  
> Updated at: 2024-08-19 22:06:23 UTC  
> Url: https://github.com/boostorg/beast/issues/2921#issuecomment-2297540128  

Beast tracks all fixes made to zlib, and the handling of this CVE is exactly why Beast does not use a system zlib. Note that the CVE regards one of the examples in the zlib repository, which Beast does not use (and does not contain). In other words Beast is unaffected.

---

## Comment 9

> Username: bgemmill  
> Created at: 2024-08-19 22:08:24 UTC  
> Url: https://github.com/boostorg/beast/issues/2921#issuecomment-2297544972  

If beast tracks fixes to zlib that's mostly what I was looking for. Thanks for the heads up.
