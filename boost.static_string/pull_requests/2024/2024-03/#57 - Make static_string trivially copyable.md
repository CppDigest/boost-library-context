# #57 Make static_string trivially copyable [Merged]

> Username: Quuxplusone  
> Created at: 2024-03-20 20:45:45 UTC  
> Updated at: 2025-10-03 16:42:11 UTC  
> Merged at: 2025-10-03 16:42:10 UTC  
> Closed at: 2025-10-03 16:42:11 UTC  
> Url: https://github.com/boostorg/static_string/pull/57  

It's not clear to me why this wasn't true from the very beginning, and `git grep trivial` doesn't enlighten me.  
`=default` requires at least C++11, but then so does `noexcept`, so that must not be a concern.  
Notice that at this point this _is_ an ABI break for `static_string`; I don't know if you care about ABI breaks.  
If you do care about ABI breaks, but would like to mark the type as trivially _relocatable_ (without an ABI break) on compilers that support P1144, let me know and I'll open a separate PR for that.

---

## Review 1 [Commented]

> Username: alandefreitas  
> Created_at: 2024-03-20 20:52:33 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/static_string/pull/57#pullrequestreview-1950126722  

📁 test/static_string.cpp

```diff
 302 |+ {
 303 |+     {
 304 |+         using S = static_string<1>;
```

> Username: alandefreitas  
> Created_at: 2024-03-20 20:52:32 UTC  
> Updated_at: 2024-03-20 20:52:33 UTC  
> Url: https://github.com/boostorg/static_string/pull/57#discussion_r1532838642  

Is   
  
```  
using S = static_string<0>;  
```  
  
a thing we need to test?

> Username: Quuxplusone  
> Created_at: 2024-03-20 21:05:34 UTC  
> Url: https://github.com/boostorg/static_string/pull/57#discussion_r1532872479  

Sure; updated.  
  
Re "That sounds great" — unclear whether you mean "great" in re "open a new PR for trivially relocatable" (which makes sense only if you don't want to take this one — trivially copyable types are trivially relocatable by definition) or just generally expressing contentment with this PR. :)

> Username: alandefreitas  
> Created_at: 2024-03-20 21:06:58 UTC  
> Url: https://github.com/boostorg/static_string/pull/57#discussion_r1532874955  

just generally expressing contentment with this PR. :)


---

## Comment 2

> Username: alandefreitas  
> Created_at: 2024-03-20 20:53:13 UTC  
> Url: https://github.com/boostorg/static_string/pull/57#issuecomment-2010603992  

That sounds great.

---

## Comment 3

> Username: vinniefalco  
> Created_at: 2024-03-21 00:07:41 UTC  
> Url: https://github.com/boostorg/static_string/pull/57#issuecomment-2010943417  

How was it not already trivially copyable...

---

## Comment 4

> Username: sdkrystian  
> Created_at: 2024-03-21 11:05:44 UTC  
> Updated_at: 2024-03-21 11:08:15 UTC  
> Url: https://github.com/boostorg/static_string/pull/57#issuecomment-2011954436  

@vinniefalco because we use character traits

---

## Comment 5

> Username: sdkrystian  
> Created_at: 2024-03-21 11:32:04 UTC  
> Url: https://github.com/boostorg/static_string/pull/57#issuecomment-2012024654  

My only reservation with this PR is that this would result in _all_ bytes being copied (regardless of `size()`... @pdimov what do you think?

---

## Comment 6

> Username: pdimov  
> Created_at: 2024-03-21 11:42:05 UTC  
> Url: https://github.com/boostorg/static_string/pull/57#issuecomment-2012047262  

This has always been the tradeoff. I prefer triviality, and memcpy with a constant N is pretty fast these days, but if we envisage static strings being often used with something like N=16384...  
  
Although on second thought, if N is 16384, then one would expect size() to be somewhere in the thousands on average, so copying will be expensive anyway.  
  
I'm pretty sure we touched this during the formal review, but I don't remember what I argued for. :-) Triviality, probably. It just makes more sense, incl. aesthetically.

---

## Comment 7

> Username: Quuxplusone  
> Created_at: 2024-03-21 12:48:23 UTC  
> Url: https://github.com/boostorg/static_string/pull/57#issuecomment-2012201357  

> My only reservation with this PR is that this would result in all bytes being copied (regardless of `size()`)...  
  
> This has always been the tradeoff. I prefer triviality, and memcpy with a constant N is pretty fast these days, but if we envisage static strings being often used with something like N=16384...  
  
Ah, I see, that's the same issue raised in `folly::small_vector` in https://github.com/facebook/folly/pull/1934#issuecomment-1954812655 . As I summarized there: _we're comparing "trivial copy (i.e. memcpy) size() elements" (saves cache lines, can't fully unroll) against "memcpy capacity() elements" (costs cache lines, can fully unroll)._  
  
Folly's answer to the tradeoff was essentially equivalent to:  
  
    static_string(const static_string&)  
      requires (sizeof(*this) <= hardware_constructive_interference_size / 2)  
      = default;  
    constexpr static_string(const static_string& rhs) noexcept  
      { assign(rhs); }  
  
I don't know if you care to do something similar here, or what, but anyway, this is now a tradeoff discussion above my pay grade. :)

---

## Comment 8

> Username: pdimov  
> Created_at: 2024-03-21 15:43:12 UTC  
> Url: https://github.com/boostorg/static_string/pull/57#issuecomment-2012706079  

They have the advantage of having the thing deployed at scale, so they can measure.  
  
I suppose we can make it trivial for N <= 64, or something like that. Conditional triviality is a pain in C++11 though.  
  
(hardware_interference_size is better left unused because it can lead to nice ABI issues as the warning says.)

---
