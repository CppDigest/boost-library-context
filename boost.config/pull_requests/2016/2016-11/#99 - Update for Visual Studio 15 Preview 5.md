# #99 Update for Visual Studio 15 Preview 5 [Merged]

> Username: MarcelRaad  
> Created at: 2016-11-14 15:42:05 UTC  
> Updated at: 2016-12-28 11:42:49 UTC  
> Merged at: 2016-11-26 17:38:47 UTC  
> Closed at: 2016-11-26 17:38:47 UTC  
> Url: https://github.com/boostorg/config/pull/99  

- Aggregate NSDMI and relaxed constexpr are supported  
- _MSC_VER is 1910  
  
All Config tests are passing and all projects using Boost I have access to compile and run fine.

---

## Comment 1

> Username: Beman  
> Created_at: 2016-11-16 18:15:12 UTC  
> Url: https://github.com/boostorg/config/pull/99#issuecomment-261025924  

I've had a number of conversions with Microsoft developers about these config macros. Two of them where the actual devs who have been processing bug reports for these features. If I recall correctly, there are a number of bug fixes that did not make it into the preview 5 release, but are supposed to be in the RC. Since the RC was announced this morning, you might want to download a copy and base the patch on the RC _MSC_VER value. I'm tied up and won't be able to do that myself for awhile.  
  
Thanks for working on this  
--Beman

---

## Comment 2

> Username: MarcelRaad  
> Created_at: 2016-11-16 18:59:28 UTC  
> Url: https://github.com/boostorg/config/pull/99#issuecomment-261038459  

Thanks @Beman ! I'll do that later today.

---

## Comment 3

> Username: MarcelRaad  
> Created_at: 2016-11-16 22:59:10 UTC  
> Url: https://github.com/boostorg/config/pull/99#issuecomment-261100367  

Updated the version number. _MSC_VER is still 1910. Everything still works.

---
