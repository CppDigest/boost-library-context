# #1 Added initial implementation of move_if_noexcept [Merged]

> Username: apolukhin  
> Created at: 2014-06-04 16:07:38 UTC  
> Updated at: 2014-08-18 00:13:06 UTC  
> Merged at: 2014-08-18 00:10:14 UTC  
> Closed at: 2014-08-18 00:10:14 UTC  
> Url: https://github.com/boostorg/move/pull/1  

This is an implementation of [move_if_noexcept](http://www.cplusplus.com/reference/utility/move_if_noexcept/) .  
  
Tests work well GCC in C++11 and C++03. Also checked move_if_noexcept with tests of Boost.CircularBuffer - works OK on GCC in C++11 and C++03 modes.  
  
Questions:  
- Is it OK to include <boost/move/traits.hpp> in <boost/move/utility.hpp> ?  
- I'm not sure about BOOST_MOVE_OLD_RVALUE_REF_BINDING_RULES part. Could you please take a closer look at it?

---

## Comment 1

> Username: chrismanning  
> Created_at: 2014-06-20 17:02:36 UTC  
> Updated_at: 2014-06-23 08:39:08 UTC  
> Url: https://github.com/boostorg/move/pull/1#discussion_r14029984  

Shouldn't all these be returning `const T&` rather than `T&`?

---

## Comment 2

> Username: apolukhin  
> Created_at: 2014-06-23 08:41:26 UTC  
> Url: https://github.com/boostorg/move/pull/1#discussion_r14065253  

Fixed that. Thanks!

---

## Comment 3

> Username: igaztanaga  
> Created_at: 2014-06-23 20:47:01 UTC  
> Url: https://github.com/boostorg/move/pull/1#issuecomment-46899688  

This seems a good addition. It makes utilities.hpp header a bit heavy now that it needs to include type traits to implement move_if_noexcept but that could be ironed splitting that header in the future. I'll merge and push it ASAP, probably this week. Many thanks for the patch.

---

## Comment 4

> Username: apolukhin  
> Created_at: 2014-08-12 16:43:31 UTC  
> Url: https://github.com/boostorg/move/pull/1#issuecomment-51941330  

Any progress? Can I help somehow?

---

## Comment 5

> Username: igaztanaga  
> Created_at: 2014-08-12 20:59:23 UTC  
> Url: https://github.com/boostorg/move/pull/1#issuecomment-51976481  

El 12/08/2014 18:43, Antony Polukhin escribió:  
  
> Any progress? Can I help somehow?  
  
Sorry Antony, I missed this with the Boost 1.56 sprint and ongoin   
holidays. I promise to work on this soon.  
  
Thanks.  
  
Ion

---

## Comment 6

> Username: igaztanaga  
> Created_at: 2014-08-18 00:13:06 UTC  
> Url: https://github.com/boostorg/move/pull/1#issuecomment-52440395  

I've merged and made some changes in commit SHA-1: 3c56780e0e67b8f16339cce5a4899b861801f031  
  
A bug in MSVC-12.0 has delayed the merge but all seems working in MSVC-7.1-12.0, GCC and Clang.  
  
Thanks for the great work.

---
