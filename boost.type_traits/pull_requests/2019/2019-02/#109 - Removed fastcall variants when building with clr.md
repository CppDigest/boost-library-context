# #109 Removed fastcall variants when building with clr [Closed]

> Username: brad-taylor  
> Created at: 2019-02-18 02:35:53 UTC  
> Updated at: 2019-06-27 07:37:17 UTC  
> Closed at: 2019-06-27 07:37:16 UTC  
> Url: https://github.com/boostorg/type_traits/pull/109  

Added missing CLR checks for fastcall variants.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2019-02-18 11:47:02 UTC  
> Url: https://github.com/boostorg/type_traits/pull/109#issuecomment-464702128  

Can you update the test cases to test this?  
  
I assume we also need this for member functions, and the docs suggest it's an x86-only calling convention?  
  
Thanks!

---

## Comment 2

> Username: brad-taylor  
> Created_at: 2019-04-04 03:25:13 UTC  
> Updated_at: 2019-04-04 03:26:31 UTC  
> Url: https://github.com/boostorg/type_traits/pull/109#issuecomment-479735564  

Hi!  
  
I believe this was already done for member functions in https://github.com/boostorg/type_traits/commit/3df3014681492fddc9800a187dcdcab749c03f6a. I found that the commit done after that merge (https://github.com/boostorg/type_traits/commit/cacb2bb3f6b52606b775d36c8952837c1209416c) appears to do the same for standard functions, but missed adding the CLR check for the noexcept variants. This is what I have addressed in this PR.  
  
I'm quite new to this library so I appreciate any feedback.  
  
Thanks

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2019-06-27 07:37:16 UTC  
> Url: https://github.com/boostorg/type_traits/pull/109#issuecomment-506230510  

Fixed in https://github.com/boostorg/type_traits/commit/efc0be6b55672720da464a923f97bc77b48117c8.

---
