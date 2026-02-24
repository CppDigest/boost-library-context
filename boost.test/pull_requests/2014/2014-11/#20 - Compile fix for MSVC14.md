# #20 Compile fix for MSVC14 [Merged]

> Username: MarcelRaad  
> Created at: 2014-11-26 21:50:00 UTC  
> Updated at: 2015-09-11 09:23:50 UTC  
> Merged at: 2014-12-11 22:18:45 UTC  
> Closed at: 2014-12-11 22:18:45 UTC  
> Url: https://github.com/boostorg/test/pull/20  

In Visual C++ 14, _set_output_format and _TWO_DIGIT_EXPONENT have been removed and are not needed anymore:  
http://blogs.msdn.com/b/vcblog/archive/2014/06/18/crt-features-fixes-and-breaking-changes-in-visual-studio-14-ctp1.aspx

---

## Comment 1

> Username: raffienficiaud  
> Created_at: 2014-12-11 22:18:57 UTC  
> Url: https://github.com/boostorg/test/pull/20#issuecomment-66699643  

Applied, thanks

---
