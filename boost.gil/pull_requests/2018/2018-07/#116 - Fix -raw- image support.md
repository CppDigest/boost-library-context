# #116 Fix "raw" image support. [Merged]

> Username: stefanseefeld  
> Created at: 2018-07-15 21:20:33 UTC  
> Updated at: 2018-07-23 12:16:58 UTC  
> Merged at: 2018-07-16 00:37:07 UTC  
> Closed at: 2018-07-16 00:37:07 UTC  
> Url: https://github.com/boostorg/gil/pull/116  

This PR fixes some issues with the "raw" IO extension. It re-enables the "raw" tests and fixes a few issues (both filenames as well as implementation details).

---

## Review 1 [Approved]

> Username: chhenning  
> Created_at: 2018-07-15 22:03:06 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/116#pullrequestreview-137279732  

Looks good. Should we rename device.hpp to io_device.hpp?

---

## Comment 2

> Username: stefanseefeld  
> Created_at: 2018-07-15 22:05:10 UTC  
> Url: https://github.com/boostorg/gil/pull/116#issuecomment-405122038  

why ? We just renamed it the other way around, from `io_device.hpp` to `io/device.hpp` !

---

## Comment 3

> Username: chhenning  
> Created_at: 2018-07-16 13:11:25 UTC  
> Url: https://github.com/boostorg/gil/pull/116#issuecomment-405242331  

Thanks Stefan!

---
