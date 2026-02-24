# #92 - BOOST_OS_LINUX not detected on some architectures [Closed]

> Username: giomasce  
> Created at: 2018-11-27 20:42:57 UTC  
> Updated at: 2018-11-27 20:44:40 UTC  
> Closed at: 2018-11-27 20:44:40 UTC  
> Url: https://github.com/boostorg/predef/issues/92  

`BOOST_OS_LINUX` is detected thanks to macros `linux` and `__linux`. However, on some architectures they are not actually defined by GCC. The macro actually suggested to use to detect Linux is `__linux__`.  
  
For example, under a recent Debian on architecture `ppc64el`:  
```  
$ g++ -std=c++11 -dM -E -x c++ /dev/null | grep linux  
#define __linux__ 1  
#define __gnu_linux__ 1  
```  
  
See https://bugs.debian.org/cgi-bin/bugreport.cgi?bug=914688 and https://gcc.gnu.org/bugzilla/show_bug.cgi?id=28314 for more context.  
  
I suggest to add `__linux__` as a macro to test for `BOOST_OS_LINUX`.

---

## Comment 1

> Username: grafikrobot  
> Created at: 2018-11-27 20:44:40 UTC  
> Url: https://github.com/boostorg/predef/issues/92#issuecomment-442210345  

https://github.com/boostorg/predef/commit/d58fcca2d59faae030d3cbeb68da6a7c63c906d7
