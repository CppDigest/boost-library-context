# #105 - Build failure with clang on Windows [Closed]

> Username: e4lam  
> Created at: 2019-11-22 04:54:11 UTC  
> Updated at: 2020-06-03 20:32:06 UTC  
> Closed at: 2020-06-02 22:56:59 UTC  
> Url: https://github.com/boostorg/interprocess/issues/105  

Using clang on Windows (clang-cl), code that uses Boost.Interprocess fails without BOOST_USE_WINDOWS_H defined because it causes an overload resolution conflict between extern "C" functions declared inside namespaces vs at global scope. So the forward declarations of the Windows API functions in boost::interprocess::winapi end up conflicting with the declarations that do show up when someone includes both boost interprocess and windows.h itself.  
  
This problem showed up in Boost.DateTime as well but it was subsequently fixed by https://github.com/boostorg/date_time/pull/45 via use of Boost.WinAPI instead of its own hand rolled redeclarations. In Boost.WinAPI, we avoid this problem by ensuring that the Windows API declarations are at the global scope but still retain the namespaced winapi methods that call the global ones.  
  
Can this be fixed? Thanks!

---

## Comment 1

> Username: igaztanaga  
> Created at: 2019-11-25 21:26:08 UTC  
> Url: https://github.com/boostorg/interprocess/issues/105#issuecomment-558347121  

Looks like Boost.Winapi does not provide all needed functions, we'll need to add the missing parts to Boost.Winapi before porting Interprocess.

---

## Comment 2

> Username: SpareSimian  
> Created at: 2019-12-19 23:51:24 UTC  
> Url: https://github.com/boostorg/interprocess/issues/105#issuecomment-567727929  

See also issue #99. I ran into this using [Sourcetrail](https://sourcetrail.com/), which uses clang under the hood to index a project for analysis. I'm mixing Interprocess and other Boost headers in the same source file, and got declaration conflicts with CreateSemaphoreA and GetSystemInfo.

---

## Comment 3

> Username: igaztanaga  
> Created at: 2020-06-03 19:37:56 UTC  
> Url: https://github.com/boostorg/interprocess/issues/105#issuecomment-638418089  

After:  
  
-  Careful use of Boost.Winapi and use of SetFilePointer instead of SetFilePointerEx (which requires a _LARGE_INTEGER by value that difficults the use of windows.h in certain circustances)  
- Added tests to verify windows.h inclusion without BOOST_USE_WINDOWS_H does not break things  
  
the issue seems to be solved. Many thanks for the report!

---

## Comment 4

> Username: e4lam  
> Created at: 2020-06-03 20:32:06 UTC  
> Url: https://github.com/boostorg/interprocess/issues/105#issuecomment-638445794  

Thank you very much!
