# #67 - Problem when cross-compiling with libc++ in mingw [Open]

> Username: jcelerier  
> Created at: 2018-10-09 08:30:21 UTC  
> Updated at: 2020-09-04 11:52:25 UTC  
> Url: https://github.com/boostorg/iostreams/issues/67  

Hello,  
I am getting the following error from my compiler, in a relatively special build configuration:    
  
```C++  
/opt/mingw-llvm-toolchain/llvm-mingw/boost_1_67_0/boost/iostreams/positioning.hpp:52:10: error: no matching constructor for initialization of 'std::streampos' (aka 'fpos<int>')  
{ return std::streampos(std::mbstate_t(), off); }  
         ^              ~~~~~~~~~~~~~~~~~~~~~  
/opt/mingw-llvm-toolchain/llvm-mingw/x86_64-w64-mingw32/include/c++/v1/string:538:31: note: candidate constructor not viable: allows at most single argument '__off', but 2 arguments were provided  
    _LIBCPP_INLINE_VISIBILITY fpos(streamoff __off = streamoff()) : __st_(), __off_(__off) {}  
                              ^  
/opt/mingw-llvm-toolchain/llvm-mingw/x86_64-w64-mingw32/include/c++/v1/string:532:28: note: candidate constructor (the implicit copy constructor) not viable: requires 1 argument, but 2 were provided  
class _LIBCPP_TEMPLATE_VIS fpos  
                           ^  
/opt/mingw-llvm-toolchain/llvm-mingw/x86_64-w64-mingw32/include/c++/v1/string:532:28: note: candidate constructor (the implicit move constructor) not viable: requires 1 argument, but 2 were provided  
  
...  
  
/opt/mingw-llvm-toolchain/llvm-mingw/boost_1_67_0/boost/iostreams/positioning.hpp:96:16: error: no member named 'seekpos' in 'std::__1::fpos<int>'  
    return pos.seekpos();  
           ~~~ ^  
```  
  
My toolchain is as follows :   
  
- Linux host  
- clang / libc++ MinGW cross-compiler (e.G. clang is a linux binary which creates windows executables with the mingw API) : basically this : https://github.com/mstorsjo/llvm-mingw  
  
Any idea of what's going on ? I suppose that `BOOST_IOSTREAMS_HAS_DINKUMWARE_FPOS` is defined when it shouldn't ?

---

## Comment 1

> Username: jcelerier  
> Created at: 2018-10-09 08:34:32 UTC  
> Url: https://github.com/boostorg/iostreams/issues/67#issuecomment-428107908  

I solve it in my own code by doing   
  
```  
#include <boost/iostreams/detail/config/fpos.hpp>  
#if defined(BOOST_IOSTREAMS_HAS_DINKUMWARE_FPOS)  
#undef BOOST_IOSTREAMS_HAS_DINKUMWARE_FPOS  
#endif  
```  
beforehand but I guess there should be a better way. Certainly the check should look for more than _YVALS now, since mingw includes yvals.h.

---

## Comment 2

> Username: jeking3  
> Created at: 2018-10-10 12:41:24 UTC  
> Url: https://github.com/boostorg/iostreams/issues/67#issuecomment-428556198  

The CI builds on AppVeyor pass, but they use the following build command (it is mingw inside msys2):  
``./b2 libs/iostreams toolset=gcc-mingw32 cxxstd=03   address-model=32   variant=release,debug -j3``  
  
I was going to suggest replacing the block you identified with the following:  
```  
#include <boost/predef/library/std.h>  
#if (BOOST_LIB_STD_DINKUMWARE)  
...  
```  
  
However after looking at the detection there, it relies on the same check for `_YVALS`.  Copying @grafikrobot since the Dinkumware detection probably needs to omit MinGW platforms.  In fact, I'd suggest looking at [the checks in iostreams](https://github.com/boostorg/iostreams/blob/develop/include/boost/iostreams/detail/config/fpos.hpp#L27) to make sure we're not missing anything else in predef.

---

## Comment 3

> Username: jcelerier  
> Created at: 2018-10-10 13:25:31 UTC  
> Url: https://github.com/boostorg/iostreams/issues/67#issuecomment-428570167  

> The CI builds on AppVeyor pass, but they use the following build command (it is mingw inside msys2):  
  
hi, compared to this configuration I'm building with clang and libc++, not gcc and libstdc++ - I guess this could be the difference ?

---

## Comment 4

> Username: SquallATF  
> Created at: 2018-12-14 12:19:04 UTC  
> Url: https://github.com/boostorg/iostreams/issues/67#issuecomment-447308772  

@jcelerier append `&& !defined(_LIBCPP_VERSION)` at https://github.com/boostorg/iostreams/blob/15e72589733152755ed0778b0bbee9f1c9bdcc84/include/boost/iostreams/detail/config/fpos.hpp#L29

---

## Comment 5

> Username: tomay3000  
> Created at: 2020-03-29 00:19:48 UTC  
> Updated at: 2020-03-29 00:22:16 UTC  
> Url: https://github.com/boostorg/iostreams/issues/67#issuecomment-605537475  

I have the same problem when compiling **v1.72** using the same toolchain [https://github.com/mstorsjo/llvm-mingw](https://github.com/mstorsjo/llvm-mingw) under Windows 7 x64 [https://github.com/boostorg/build/issues/557](https://github.com/boostorg/build/issues/557)   
  
So I have to apply the patch and see!  
  
BTW, what command lines u used to bootstrap and compile it?  
  
TIA.

---

## Comment 6

> Username: Gei0r  
> Created at: 2020-09-04 11:52:25 UTC  
> Url: https://github.com/boostorg/iostreams/issues/67#issuecomment-687096533  

I face the same problem in v1.74 using clang and libc++ on Windows.  
https://github.com/boostorg/iostreams/pull/106 fixes it. Please merge it.
