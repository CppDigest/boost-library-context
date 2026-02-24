# #28 - _SILENCE_CXX17_OLD_ALLOCATOR_MEMBERS_DEPRECATION_WARNING [Closed]

> Username: jdx-john  
> Created at: 2021-08-10 18:22:43 UTC  
> Updated at: 2022-11-01 20:36:02 UTC  
> Closed at: 2022-11-01 20:36:01 UTC  
> Url: https://github.com/boostorg/unordered/issues/28  

I'm not familiar with boost codebase multi-lib structure so apologies if this is in wrong sub-repo, but I get lots of these errors after upgrading to latest WinSDK 10.0.19041.0 version (I have no idea why 10.0.18xx didn't raise it):  
  
> include\boost\unordered\detail\implementation.hpp(1452,7): error C4996: 'std::allocator<T>::is_always_equal': warning STL4010: Various members of std::allocator are deprecated in C++17. Use std::allocator_traits instead of accessing these members directly. You can define _SILENCE_CXX17_OLD_ALLOCATOR_MEMBERS_DEPRECATION_WARNING or _SILENCE_ALL_CXX17_DEPRECATION_WARNINGS to acknowledge that you have received this warning.  
  
I saw https://github.com/boostorg/regex/issues/67 - is this something which is not consistent across different boost libs? I've fixed it in my project but was surprised to come across it.

---

## Comment 1

> Username: droberts195  
> Created at: 2022-07-27 08:29:51 UTC  
> Url: https://github.com/boostorg/unordered/issues/28#issuecomment-1196420727  

I think this is referring to the same problem as #52, and if so it was fixed by https://github.com/boostorg/unordered/commit/a6b9fb285c6ffb3abd5241c5ea40bca13aa7c794.

---

## Comment 2

> Username: cmazakas  
> Created at: 2022-11-01 20:36:01 UTC  
> Url: https://github.com/boostorg/unordered/issues/28#issuecomment-1299107116  

Seems all is resolved! Marking as closed.
