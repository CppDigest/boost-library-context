# #3014 - zlib-ng for websocket permessage-deflate? [Open]

> Username: bgemmill  
> Created at: 2025-05-29 13:57:52 UTC  
> Updated at: 2025-08-24 13:20:57 UTC  
> Url: https://github.com/boostorg/beast/issues/3014  

In a prior issue, [I was asking about using a system zlib](https://github.com/boostorg/beast/issues/2921), or boost::iostreams when the motivating example was CVE tracking.  
  
I'm working on a piece of code now that consumes a really large amount of compressed websocket messages, and spends a large amount of run time decompressing them in beast's zlib implementation according to `perf`.  
  
This piece of code is performance sensitive, and I'd like to try using [zlib-ng](https://github.com/zlib-ng/zlib-ng) or [Cloudflare's fork](https://github.com/cloudflare/zlib) to see about speeding that up.  
  
Is there any way to make a websocket connect with permessage deflate and then let the user supply the implementation?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2025-05-31 15:27:07 UTC  
> Url: https://github.com/boostorg/beast/issues/3014#issuecomment-2925329570  

That depends, is the zlib-ng API identical to regular zlib?

---

## Comment 2

> Username: bgemmill  
> Created at: 2025-06-01 12:22:31 UTC  
> Url: https://github.com/boostorg/beast/issues/3014#issuecomment-2927138369  

zlib-ng's API is identical (or they take bug reports) if compiled in [zlib-compat mode](https://github.com/zlib-ng/zlib-ng/blob/develop/PORTING.md), which provides a [zlib.h](https://github.com/zlib-ng/zlib-ng/blob/develop/zlib.h.in).

---

## Comment 3

> Username: bgemmill  
> Created at: 2025-07-05 07:38:38 UTC  
> Url: https://github.com/boostorg/beast/issues/3014#issuecomment-3038362274  

@vinniefalco is there a good way to approach this as a user of beast, or does this require beast to be changed to use `zlib.h`?  
  
If the second, happy to have a go at a PR if you can point me in the right direction.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2025-08-24 13:20:57 UTC  
> Url: https://github.com/boostorg/beast/issues/3014#issuecomment-3218101594  

Yes, in theory Beast could be refactored to acquire the compressor from some singleton that can be reconfigured, if the APIs are the same.
