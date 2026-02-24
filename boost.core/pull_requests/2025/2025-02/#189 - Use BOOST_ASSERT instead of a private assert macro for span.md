# #189 Use BOOST_ASSERT instead of a private assert macro for span [Open]

> Username: pdimov  
> Created at: 2025-02-24 01:04:35 UTC  
> Updated at: 2025-02-27 19:53:28 UTC  
> Url: https://github.com/boostorg/core/pull/189  

Instead of removing assertions under GCC 4.x to retain constexpr support, this retains the assertions and removes constexpr support under GCC 4.x instead. The presumption is that GCC 4.x users care more about assertions than they care about constexpr (which is hardly useful there anyway.)

---

## Comment 1

> Username: glenfe  
> Created_at: 2025-02-24 23:19:24 UTC  
> Updated_at: 2025-02-24 23:26:08 UTC  
> Url: https://github.com/boostorg/core/pull/189#issuecomment-2679910404  

I would rather just drop GCC 4 support in span instead.  
  
(In +2 releases after announcing it)

---

## Comment 2

> Username: pdimov  
> Created_at: 2025-02-25 00:29:48 UTC  
> Url: https://github.com/boostorg/core/pull/189#issuecomment-2680025418  

One does not preclude the other.

---

## Comment 3

> Username: glenfe  
> Created_at: 2025-02-25 05:47:11 UTC  
> Updated_at: 2025-02-25 05:50:53 UTC  
> Url: https://github.com/boostorg/core/pull/189#issuecomment-2680665431  

Adding the asserts in the way I did was a non-breaking change. The first breaking change I want to make is dropping GCC 4 support. I'm happy to make it sooner.  
  
(Until then I'm fine with GCC 4 users not having asserts)

---

## Comment 4

> Username: pdimov  
> Created_at: 2025-02-25 13:39:59 UTC  
> Url: https://github.com/boostorg/core/pull/189#issuecomment-2682016243  

It's not a breaking change if it doesn't break anything. :-)

---

## Comment 5

> Username: pdimov  
> Created_at: 2025-02-27 19:53:27 UTC  
> Url: https://github.com/boostorg/core/pull/189#issuecomment-2688975936  

Regardless of whether this is applied, develop needs to be merged to master.  
  
> (Until then I'm fine with GCC 4 users not having asserts)  
  
That's fine in principle, but in this specific case, `span` has only one purpose in life and it's to assert. :-)

---
