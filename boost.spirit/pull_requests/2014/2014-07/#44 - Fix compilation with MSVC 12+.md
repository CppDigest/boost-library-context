# #44 Fix compilation with MSVC 12+ [Merged]

> Username: MarcelRaad  
> Created at: 2014-07-05 18:05:40 UTC  
> Updated at: 2014-07-07 04:31:20 UTC  
> Merged at: 2014-07-07 02:12:44 UTC  
> Closed at: 2014-07-07 02:12:44 UTC  
> Url: https://github.com/boostorg/spirit/pull/44  

Visual C++ wrongly always chooses the variadic template version of a function if there are variadic and non-variadic template versions:  
https://connect.microsoft.com/VisualStudio/feedback/details/806150/vc-12-variadic-template-function-preferred-over-non-variadic-template-function  
  
This affects at least Visual C++ 2013 RTM and Update 1, the November 2013 CTP and Visual C++ 14 CTP1.  
  
This commit works around that bug by only enabling the variadic template version of is_weak_substitute and is_container for 2+ template arguments.

---

## Comment 1

> Username: djowel  
> Created_at: 2014-07-05 21:44:30 UTC  
> Url: https://github.com/boostorg/spirit/pull/44#issuecomment-48097226  

Have you tested this against other compilers apart from Visual C++ 14 CTP1? If this workaround is only for VS then it probably should be fenced inside #ifdefs

---

## Comment 2

> Username: K-ballo  
> Created_at: 2014-07-05 21:59:14 UTC  
> Url: https://github.com/boostorg/spirit/pull/44#issuecomment-48097502  

Wouldn't this change call for extra specializations to handle the `variant<T0>` case?

---

## Comment 3

> Username: MarcelRaad  
> Created_at: 2014-07-06 19:31:30 UTC  
> Updated_at: 2014-07-06 20:01:05 UTC  
> Url: https://github.com/boostorg/spirit/pull/44#issuecomment-48122541  

Thanks for the comments!  
  
I have tested this with Visual C++ 14 CTP1 and 2013 Update 1. I think the issue is claimed to be fixed in Update 2, but unfortunately I can't update because of a lot of serious regressions with Internal Compiler Errors. Unfortunately I currently don't have access to non-Windows machines, but I can't see how this could break other compilers.  
  
An #ifdef could of course be added, but I don't think it is necessary as the change shouldn't affect other compilers at all. The problem also affected Boost.Signals2 and it was fixed without #ifdefs:  
https://github.com/boostorg/signals2/commit/a4cf126ad1c973ffe52a7a73b20c682ec22a4503  
  
The `variant<T>` case is handled in the lines above the changes, unfortunately GitHub doesn't show them.

---

## Comment 4

> Username: K-ballo  
> Created_at: 2014-07-07 02:12:49 UTC  
> Url: https://github.com/boostorg/spirit/pull/44#issuecomment-48135501  

Thanks!

---
