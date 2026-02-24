# #22 remove unaligned loads and stores on x86 [Closed]

> Username: arvidn  
> Created at: 2017-11-04 12:04:21 UTC  
> Updated at: 2017-12-23 20:24:00 UTC  
> Closed at: 2017-12-23 19:06:22 UTC  
> Url: https://github.com/boostorg/endian/pull/22  

It invokes undefined behavior regardless of the compiler back-end. more details in #21

---

## Comment 1

> Username: pdimov  
> Created_at: 2017-12-20 16:45:48 UTC  
> Url: https://github.com/boostorg/endian/pull/22#issuecomment-353116531  

Instead of removing the #ifdefs altogether, why not use memcpy there?

---

## Comment 2

> Username: arvidn  
> Created_at: 2017-12-20 18:30:51 UTC  
> Url: https://github.com/boostorg/endian/pull/22#issuecomment-353145070  

You mean as an optimisation of ``unrolled_byte_loops`` when no endian conversion is necessary?  
I think such optimisation is orthogonal to the current special-case, since it should:  
  
1. be applied based on the host endianness (not specific architecture)  
2. be applied for big-endian types on big endian hosts

---

## Comment 3

> Username: pdimov  
> Created_at: 2017-12-20 18:32:26 UTC  
> Url: https://github.com/boostorg/endian/pull/22#issuecomment-353145423  

I mean as a replacement for the current casts, as this would minimize the patch both in lines and in spirit.

---

## Comment 4

> Username: pdimov  
> Created_at: 2017-12-21 14:27:00 UTC  
> Url: https://github.com/boostorg/endian/pull/22#issuecomment-353363908  

> 1. be applied based on the host endianness (not specific architecture)  
  
It's not clear whether it will be an optimization. It's possible for the `unrolled_byte_loops` to be more efficient if `memcpy` is not an intrinsic but compiles to a function call. We'll have to check on godbolt.org.  
  
For x86 I think that all major compilers recognize `memcpy` on 4/8 bytes.

---

## Comment 5

> Username: pdimov  
> Created_at: 2017-12-21 14:39:18 UTC  
> Url: https://github.com/boostorg/endian/pull/22#issuecomment-353366803  

Here's a testbed: https://godbolt.org/g/MfP3Hc

---

## Comment 6

> Username: pdimov  
> Created_at: 2017-12-21 14:39:41 UTC  
> Url: https://github.com/boostorg/endian/pull/22#issuecomment-353366887  

At least for MSVC, removing the special case notably degrades the code.

---

## Comment 7

> Username: arvidn  
> Created_at: 2017-12-21 14:52:04 UTC  
> Url: https://github.com/boostorg/endian/pull/22#issuecomment-353369989  

I can put in ``memcpy`` there instead. It's tempting to depend on endian-macros instead of architecture macros though, and to use ``memcpy`` for the big endian case (on big endian machines).  
  
It seems like a pretty safe assumption that ``memcpy`` is more efficient than a hand-written copy loop, in general. It's likely an easier optimisation to make.

---

## Comment 8

> Username: pdimov  
> Created_at: 2017-12-21 16:00:18 UTC  
> Url: https://github.com/boostorg/endian/pull/22#issuecomment-353387620  

As tested on godbolt, all x86 compilers (a) benefit significantly from the special case and (b) generate the same code with the current one and with `memcpy`.  
  
ARM however generates a function call to `memcpy` so I'm not sure that the special case should be applied there.  
  
Looks like the safest choice is to keep the current #ifdefs and just replace the cast inside with `memcpy`.

---

## Comment 9

> Username: pdimov  
> Created_at: 2017-12-23 19:06:21 UTC  
> Url: https://github.com/boostorg/endian/pull/22#issuecomment-353742886  

Test added in https://github.com/boostorg/endian/commit/62802fee96f002134719d02158c6fe859847fd9b. Fixed in https://github.com/boostorg/endian/commit/e93f6a22703249f0ebfff25302147c64965d5907.

---
