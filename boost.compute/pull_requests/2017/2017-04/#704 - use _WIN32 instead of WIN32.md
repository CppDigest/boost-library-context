# #704 use _WIN32 instead of WIN32 [Merged]

> Username: huanzhang12  
> Created at: 2017-04-12 23:41:26 UTC  
> Updated at: 2017-04-13 04:40:13 UTC  
> Merged at: 2017-04-13 04:40:13 UTC  
> Closed at: 2017-04-13 04:40:13 UTC  
> Url: https://github.com/boostorg/compute/pull/704  

In two places of Boost.Compute, the macro WIN32 is used to detect Windows environment.  
However, this macro is undefined on g++ when C++11 or C++0x is used. This can cause a crash in detail/path.hpp, because the incorrect path will be used.  
  
There are some discussions on the Internet regarding this issue:  
  
https://github.com/RcppCore/Rcpp/issues/360  
https://sourceforge.net/p/mingw-w64/discussion/723798/thread/4ce97145/  
  
I tried to compile the following code in MinGW, with GCC 6.3.0  
```  
#ifndef WIN32  
        #error Define WIN32 is missing!  
#endif  
#ifndef _WIN32  
        #error Define _WIN32 is missing!  
#endif  
int  
main()  
{  
        return 0;  
}  
```  
  
And when c++11 or c++0x is used, WIN32 will not be defined:  
  
```  
D:\github_repos>g++ -o test test.cpp  
  
D:\github_repos>g++ -std=c++11 -o test test.cpp  
test.cpp:2:10: error: #error Define WIN32 is missing!  
         #error Define WIN32 is missing!  
          ^~~~~  
  
D:\github_repos>g++ -std=c++0x -o test test.cpp  
test.cpp:2:10: error: #error Define WIN32 is missing!  
         #error Define WIN32 is missing!  
          ^~~~~  
```

---

## Comment 1

> Username: coveralls  
> Created_at: 2017-04-13 00:45:50 UTC  
> Url: https://github.com/boostorg/compute/pull/704#issuecomment-293746133  

[![Coverage Status](https://coveralls.io/builds/11065313/badge)](https://coveralls.io/builds/11065313)  
  
Coverage remained the same at 83.361% when pulling **11c5e4af5cfb02fbb314ada3a99577ac4b31d2f7 on huanzhang12:develop** into **139b947c34c0c4ba027592c301797cbd7387e024 on boostorg:develop**.

---

## Comment 2

> Username: kylelutz  
> Created_at: 2017-04-13 04:39:58 UTC  
> Url: https://github.com/boostorg/compute/pull/704#issuecomment-293779062  

Thanks for the patch! At some point I think we should switch over to using the [Boost.Predef](http://www.boost.org/doc/libs/1_63_0/doc/html/predef.html) macros for this.

---
