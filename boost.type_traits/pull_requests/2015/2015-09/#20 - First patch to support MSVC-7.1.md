# #20 First patch to support MSVC-7.1 [Merged]

> Username: igaztanaga  
> Created at: 2015-09-19 20:36:28 UTC  
> Updated at: 2015-09-24 12:00:21 UTC  
> Merged at: 2015-09-24 11:59:48 UTC  
> Closed at: 2015-09-24 11:59:48 UTC  
> Url: https://github.com/boostorg/type_traits/pull/20  

Trivial patches to support MSVC-7.1. After these, only common_type related two tests fail, a problem that requires more time and an additional patch. Information about the patch:  
  
-> MSVC7-1 does not support warnings above 4999. This problem was already workarounded in some files  using BOOST_WORKAROUND(BOOST_MSVC_FULL_VER, >= 140050000), which is what I've done.  
-> composite_pointer_type requires two steps to form a pointer, so that MSVC-7.1 can correctly handle it.

---

## Comment 1

> Username: igaztanaga  
> Created_at: 2015-09-19 20:44:59 UTC  
> Url: https://github.com/boostorg/type_traits/pull/20#issuecomment-141706163  

Sorry, I missed two more changes:  
  
-> In is_convertible.hpp, MSVC was forming a reference to an array of unknown bound. I still don't understand how that is avoided in the rest of compilers when test is_convertible_basic_impl  
  
-> In is_signed/is_unsigned, I didn't found a workaround quickly but I saw other compilers were also using the workaround that avoids detecting generic unsigned/signed types, so I just added MSVC-7.1 to the group.

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2015-09-24 12:00:20 UTC  
> Url: https://github.com/boostorg/type_traits/pull/20#issuecomment-142909335  

Thanks!

---
