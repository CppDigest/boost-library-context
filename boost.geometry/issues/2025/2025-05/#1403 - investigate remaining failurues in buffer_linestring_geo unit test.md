# #1403 - investigate remaining failurues in buffer_linestring_geo unit test [Open]

> Username: vissarion  
> Created at: 2025-05-02 12:25:02 UTC  
> Updated at: 2025-05-13 20:19:27 UTC  
> Url: https://github.com/boostorg/geometry/issues/1403  

Tests are failing due to an error in buffer_linestring_geo unit tests.   
After bisecting the first commit that breaks that test is https://github.com/boostorg/geometry/commit/805ff654e869030c4ba70f37fad6c3825f59c077  
Interestingly, the cmake tests are passing since that test is disactivated (should we enable it by default?).   
  
**edit**: the above is fixed. What is remaining is:  
> I will investigate them later this year, after integrating some other enhancements (robust side, https://github.com/boostorg/geometry/pull/1404, ...)

---

## Comment 1

> Username: barendgehrels  
> Created at: 2025-05-09 15:35:51 UTC  
> Url: https://github.com/boostorg/geometry/issues/1403#issuecomment-2867018925  

Thanks I'll have a look
