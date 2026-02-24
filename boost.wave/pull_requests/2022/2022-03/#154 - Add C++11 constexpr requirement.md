# #154 Add C++11 constexpr requirement [Merged]

> Username: Flamefire  
> Created at: 2022-03-09 15:20:50 UTC  
> Updated at: 2022-03-09 17:17:35 UTC  
> Merged at: 2022-03-09 16:34:53 UTC  
> Closed at: 2022-03-09 16:34:53 UTC  
> Url: https://github.com/boostorg/wave/pull/154  

This is required as of https://github.com/boostorg/wave/blob/develop/build/Jamfile.v2#L15  
  
Add a VS2013 job which would otherwise fail, see #153  
  
Note that VS2013 is NOT supported by Boost.Wave anymore due to incomplete C++11 support. The added requirement makes it skip building Boost.Wave on that (and possibly other affected) compiler(s) avoiding a hard error.

---

## Comment 1

> Username: jefftrull  
> Created_at: 2022-03-09 16:34:45 UTC  
> Url: https://github.com/boostorg/wave/pull/154#issuecomment-1063118268  

Thanks for noticing this and for the fix!

---
