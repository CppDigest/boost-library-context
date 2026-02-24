# #146 Clean up sprintf usage [Merged]

> Username: jefftrull  
> Created at: 2022-01-27 01:25:30 UTC  
> Updated at: 2022-01-28 23:04:20 UTC  
> Merged at: 2022-01-28 23:04:20 UTC  
> Closed at: 2022-01-28 23:04:20 UTC  
> Url: https://github.com/boostorg/wave/pull/146  

There have been compiler warnings about `sprintf` usage for some time, and now we have a bug report as well (#145). I replaced them mostly with `boost::format` but in one case with `std::to_string` (our second use of a C++11 feature!).  
  
I also did a few cleanups while I was in the code - indentation and one unused header.

---

## Review 1 [Approved]

> Username: hkaiser  
> Created_at: 2022-01-27 02:51:16 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/wave/pull/146#pullrequestreview-864325632  

LGTM, thanks!

---
