# #158 Fix up C++03 deprecation [Merged]

> Username: jefftrull  
> Created at: 2022-03-09 22:53:58 UTC  
> Updated at: 2022-03-10 03:49:56 UTC  
> Merged at: 2022-03-10 03:49:56 UTC  
> Closed at: 2022-03-10 03:49:56 UTC  
> Url: https://github.com/boostorg/wave/pull/158  

- Add C++11 feature requirements to test and samples subprojects  
- Add `constexpr` to the features tested for in the deprecation check  
- Add the C++11 requirement to the documentation metadata  
- Add one C++03 build to the Appveyor list. It should pass without running (serves as a test on the above).

---

## Comment 1

> Username: jefftrull  
> Created_at: 2022-03-09 22:54:47 UTC  
> Url: https://github.com/boostorg/wave/pull/158#issuecomment-1063461667  

This is kind of a fix for the 1.79 release. I intend to merge it tonight (the deadline) if there are no objections

---
