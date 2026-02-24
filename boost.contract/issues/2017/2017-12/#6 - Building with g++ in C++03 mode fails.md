# #6 - Building with g++ in C++03 mode fails [Closed]

> Username: pdimov  
> Created at: 2017-12-17 18:01:06 UTC  
> Updated at: 2017-12-19 02:56:38 UTC  
> Closed at: 2017-12-19 02:56:38 UTC  
> Url: https://github.com/boostorg/contract/issues/6  

Building the library with f.ex. msvc-8.0, or 10.0, works; and so does building with g++ in C++11 mode. However, building with g++ in C++03 mode fails:  
```  
C:\Projects\boost-git\boost\libs\contract>b2 build toolset=gcc cxxstd=03  
Performing configuration checks  
  
    - 32-bit                   : yes (cached)  
    - arm                      : no  (cached)  
    - mips1                    : no  (cached)  
    - power                    : no  (cached)  
    - sparc                    : no  (cached)  
    - x86                      : yes (cached)  
    - symlinks supported       : no  (cached)  
    - junctions supported      : yes (cached)  
    - hardlinks supported      : yes (cached)  
...patience...  
...patience...  
...found 2483 targets...  
...updating 8 targets...  
gcc.compile.c++ ..\..\bin.v2\libs\contract\build\gcc-gnu-6.4.0\debug\cxxstd-03-i  
so\threadapi-win32\contract.o  
In file included from ..\../boost/contract/detail/checking.hpp:11:0,  
                 from ..\../boost/contract/old.hpp:17,  
                 from ..\../boost/contract/detail/inlined/old.hpp:13,  
                 from ..\../boost/contract/detail/inlined.hpp:10,  
                 from ..\..\libs\contract\src\contract.cpp:11:  
..\../boost/contract/detail/static_local_var.hpp:19:71: error: a cast to a type  
other than an integral or enumeration type cannot appear in a constant-expressio  
n  
 template<typename Tag, typename T, typename Init = none*, Init init = Init()>  
                                                                       ^~~~~~  
..\../boost/contract/detail/static_local_var.hpp:28:31: error: template argument  
 4 is invalid  
 struct static_local_var<Tag, T> {  
                               ^  
```  
and so on.

---

## Comment 1

> Username: lcaminiti  
> Created at: 2017-12-18 05:49:27 UTC  
> Url: https://github.com/boostorg/contract/issues/6#issuecomment-352332215  

Yes, thanks for reporting this. I actually noted it too just other day. GCC/Clang C++03 get confused on a template specialization that I recently introduced. I removed the specialization fixing this on my local copy.  
  
I'm running some other tests now. I'll be checking the fix in the next few days.

---

## Comment 2

> Username: lcaminiti  
> Created at: 2017-12-19 02:56:38 UTC  
> Url: https://github.com/boostorg/contract/issues/6#issuecomment-352625134  

Fix this in the last commit (split template specializations into 2 separate templates so that works on GCC/Clang C++03).
