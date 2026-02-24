# #30 fix build error on windows with boost::mutex and unique_lock [Closed]

> Username: lc-Arvin  
> Created at: 2018-09-11 13:21:59 UTC  
> Updated at: 2022-05-30 08:24:06 UTC  
> Closed at: 2022-05-30 08:24:06 UTC  
> Url: https://github.com/boostorg/locale/pull/30  

when compiling on windows (vs2017/vs2015), there are errors:  
locale\src\win32\lcid.cpp(89): error C2039: “unique_lock”: not a member of boost  
locale\src\win32\lcid.cpp(89): error C2065: “unique_lock”: undefined  
locale\src\win32\lcid.cpp(89): error C2275: “boost::mutex”: illegal  
locale\src\win32\lcid.cpp(89): error C3861: “lock”: cannot find  
  
errors are fixed by adding the proper header.

---
