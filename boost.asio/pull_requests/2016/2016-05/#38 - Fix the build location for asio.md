# #38 Fix the build location for asio. [Merged]

> Username: danieljames  
> Created at: 2016-05-29 12:06:27 UTC  
> Updated at: 2016-07-09 11:56:36 UTC  
> Merged at: 2016-07-09 11:56:36 UTC  
> Closed at: 2016-07-09 11:56:36 UTC  
> Url: https://github.com/boostorg/asio/pull/38  

Boost build has changed so that documentation is built relative to the Jamfile, rather than the current working directory. This broke the ASIO documentation build, to fix it specify the location using the 'name'  
parameter - which is a bit wonky, but it's what boost build looks for.

---

## Comment 1

> Username: danieljames  
> Created_at: 2016-07-09 11:56:28 UTC  
> Url: https://github.com/boostorg/asio/pull/38#issuecomment-231530793  

It's been a while since I posted this, so I'm just going to merge it myself.

---
