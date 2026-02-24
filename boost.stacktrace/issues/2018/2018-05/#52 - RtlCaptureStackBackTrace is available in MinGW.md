# #52 - RtlCaptureStackBackTrace is available in MinGW [Closed]

> Username: volo-zyko  
> Created at: 2018-05-02 12:28:49 UTC  
> Updated at: 2018-05-23 19:31:07 UTC  
> Closed at: 2018-05-23 19:31:07 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/52  

At least in my case (GCC 6.3.0, sjlj exception handling, Window 8.1 Pro 64-bit, build targeting 32-bit environment) it compiles and works fine. On the contrary _Unwind_Backtrace in the same environment compiles but does not work.  
  
It would be good if this were at least configurable if there are still cases when RtlCaptureStackBackTrace is not available.  
  
https://github.com/boostorg/stacktrace/blob/e4e2d4c3c130c74f369655c1da204a64fce9937a/include/boost/stacktrace/safe_dump_to.hpp#L214

---

## Comment 1

> Username: volo-zyko  
> Created at: 2018-05-02 12:59:57 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/52#issuecomment-385969219  

Sorry, I was wrong. I use MinGW-w64 targeting 32-bit environment, hence the confusion. And I work with boost 1.65.1 which had in this line `!defined(BOOST_GCC)`. Changing this code to `!defined(BOOST_WINAPI_IS_MINGW)` fixed my issue.  
  
This one can be closed as invalid.

---

## Comment 2

> Username: apolukhin  
> Created at: 2018-05-23 19:31:07 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/52#issuecomment-391469159  

Anyway, thanks for the second comment! It saved me from debugging :)
