# #152 Fix missing define [Closed]

> Username: sphawk  
> Created at: 2022-03-26 04:04:58 UTC  
> Updated at: 2022-03-26 17:35:49 UTC  
> Closed at: 2022-03-26 12:26:38 UTC  
> Url: https://github.com/boostorg/nowide/pull/152  

add missing define NOWIDE_USE_WCHAR_OVERLOADS on standalone

---

## Comment 1

> Username: Flamefire  
> Created_at: 2022-03-26 10:45:21 UTC  
> Updated_at: 2022-03-26 10:51:26 UTC  
> Url: https://github.com/boostorg/nowide/pull/152#issuecomment-1079660044  

Thanks for catching this. I'll see how I can avoid such issues in the future.  
  
However all PRs should be against the `develop` branch. The `standalone` branch is auto-generated from that.  
  
Out of curiosity: In which context/use case did you found that?

---

## Comment 2

> Username: sphawk  
> Created_at: 2022-03-26 12:23:02 UTC  
> Updated_at: 2022-03-26 12:25:18 UTC  
> Url: https://github.com/boostorg/nowide/pull/152#issuecomment-1079676238  

I found it from using nowide::ifstream  
there is "#if NOWIDE_USE_WCHAR_OVERLOADS"  
and occured warning,   
compiler complain there is not exist define NOWIDE_USE_WCHAR_OVERLOADS  
and my compilation setting treats warning as error.  
  
May I close this pull request? or please close this.  
Thanks.

---

## Comment 3

> Username: Flamefire  
> Created_at: 2022-03-26 12:26:35 UTC  
> Url: https://github.com/boostorg/nowide/pull/152#issuecomment-1079681675  

Ok thanks! I assume you refer to `-Wundef` as the warning that occurs. I'll include that in the CI tests and make sure the define is properly set.

---

## Comment 4

> Username: Flamefire  
> Created_at: 2022-03-26 16:47:07 UTC  
> Url: https://github.com/boostorg/nowide/pull/152#issuecomment-1079730710  

The standalone branch was just updated after merging the fix which now includes this define: https://github.com/boostorg/nowide/blob/standalone/include/nowide/config.hpp#L73-L79  
  
I hope this works for you and thanks for reporting this!

---

## Comment 5

> Username: sphawk  
> Created_at: 2022-03-26 17:35:49 UTC  
> Url: https://github.com/boostorg/nowide/pull/152#issuecomment-1079739996  

@Flamefire It works! thanks a lot!

---
