# #784 Redeclaration of max_factorial value [Merged]

> Username: ckormanyos  
> Created at: 2022-05-10 06:33:10 UTC  
> Updated at: 2022-05-10 17:47:42 UTC  
> Merged at: 2022-05-10 17:45:50 UTC  
> Closed at: 2022-05-10 17:45:50 UTC  
> Url: https://github.com/boostorg/math/pull/784  

I found this one while snooping around with older compiler(s) and other projects. But it also influences Math/Multiprecision directly.  
  
This minute change raises the question of exactly _how_ should we deal with `static` `constexpr` POD members of a class/struct? Even in the presence of `BOOST_NO_INCLASS_MEMBER_INITIALIZATION` do these really need out-of-class initialization for any of the compilers we are using?  
  
That there is a mismatch between `const` and `constexpr` is truly wrong. But what is right or less wrong?  
  
I addressed this question in my own developments about two years ago while modernizing and came to the conclusion that the extra out-of-class initialization is redundant, harmless and somehow is handled properly (or simply disregarded) by every old, new, embedded, etc. compiler that I use from 11, 14, 17, to 20 and beyond. So I synchronized the word `constexpr` and retained all such initializations in my projects.  
  
So within the context of this minute change in this PR, maybe some dialog regarding our philosophy in Boost will ensue?  
  
Cc: @jzmaddock and @mborland and @NAThompson.

---

## Comment 1

> Username: ckormanyos  
> Created_at: 2022-05-10 14:26:36 UTC  
> Url: https://github.com/boostorg/math/pull/784#issuecomment-1122470712  

This few-letter change is ready for review/discussion

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2022-05-10 16:05:56 UTC  
> Url: https://github.com/boostorg/math/pull/784#issuecomment-1122595651  

Looks fine to me.

---

## Comment 3

> Username: ckormanyos  
> Created_at: 2022-05-10 17:47:38 UTC  
> Url: https://github.com/boostorg/math/pull/784#issuecomment-1122693250  

> Looks fine to me.  
  
Thank you , John!  
  
See also [Multiprecison 429](https://github.com/boostorg/multiprecision/issues/429)

---
