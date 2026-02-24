# #2147 Reduce exe size: [Closed]

> Username: madmongo1  
> Created at: 2021-01-11 17:23:08 UTC  
> Updated at: 2022-06-21 23:39:33 UTC  
> Closed at: 2022-06-21 23:39:33 UTC  
> Url: https://github.com/boostorg/beast/pull/2147  

Use polymorphic buffers type to reduce template expansions.

---

## Comment 1

> Username: stale[bot]  
> Created_at: 2021-05-29 16:10:40 UTC  
> Url: https://github.com/boostorg/beast/pull/2147#issuecomment-850857784  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 2

> Username: edtanous  
> Created_at: 2021-10-27 02:48:53 UTC  
> Updated_at: 2021-10-27 03:11:34 UTC  
> Url: https://github.com/boostorg/beast/pull/2147#issuecomment-952489992  

I pulled these patches into bmcweb (a component of openbmc), a heavy beast user with fairly stringent binary size requirements.  When measuring a size optimized, compressed, stripped, binary size with LTO enabled, this still drops the binary size by about 5% compared to where they were branched from (1307571 bytes down to 1242468), which, assuming this has no functional impact, would be great to get on master.  What is the state of these patches?

---

## Comment 3

> Username: vinniefalco  
> Created_at: 2021-10-27 05:24:05 UTC  
> Url: https://github.com/boostorg/beast/pull/2147#issuecomment-952549263  

only 5% though, that means something to you?

---

## Comment 4

> Username: vinniefalco  
> Created_at: 2021-10-27 05:26:31 UTC  
> Url: https://github.com/boostorg/beast/pull/2147#issuecomment-952550131  

This can't go in for 1.78 but we can consider it for 1.79. However, two things:  
  
1. Prefer the term `any` over `polymorphic`  
2. The type-erasing wrapper has to maintain a copy of the original _BufferSequence_ for the lifetime of the wrapper.  
  
(2) is not stated explicilty in Asio but @chriskohlhoff said so.

---

## Comment 5

> Username: edtanous  
> Created_at: 2021-10-27 05:35:59 UTC  
> Url: https://github.com/boostorg/beast/pull/2147#issuecomment-952553915  

> only 5% though, that means something to you?  
  
In our case, the whole firmware (linux and all of userspace) has to fit in 32MB, and so we're basically continuously at the limit.  Every 50K we save somewhere means we can fit that many more features in somewhere else.  5% isn't a ton, but 5% here and there repeated a couple times across a few applications starts to add up over time.

---

## Comment 6

> Username: chriskohlhoff  
> Created_at: 2021-10-27 06:05:08 UTC  
> Url: https://github.com/boostorg/beast/pull/2147#issuecomment-952567780  

> 2. The type-erasing wrapper has to maintain a copy of the original BufferSequence for the lifetime of the wrapper.  
>  
> (2) is not stated explicilty in Asio but @chriskohlhoff said so.  
  
The requirement is documented here:  
  
https://www.boost.org/doc/libs/develop/doc/html/boost_asio/reference/read_write_operations.html  
  
When I looked into this before, I identified a common buffer sequence representation for all writes, and limited instantiations that way. My pull request was only for websockets, however, and HTTP has not had that treatment AFAIK.  
  
https://github.com/boostorg/beast/pull/2169/files

---

## Comment 7

> Username: stale[bot]  
> Created_at: 2022-01-09 01:16:30 UTC  
> Url: https://github.com/boostorg/beast/pull/2147#issuecomment-1008206034  

This issue has been open for a while with no activity, has it been resolved?

---
