# #116 - MinGW32 compilation errors [Closed]

> Username: Lastique  
> Created at: 2024-01-18 21:41:59 UTC  
> Updated at: 2025-12-01 10:14:14 UTC  
> Closed at: 2025-12-01 10:14:14 UTC  
> Url: https://github.com/boostorg/system/issues/116  

Looks like 44582f3c3cc73c1a28919ad030de1106cade3e64 and possibly some other recent commits broke MinGW32 builds of Boost.Filesystem:  
  
```  
gcc.compile.c++ bin.v2\libs\filesystem\build\gcc-5\debug\cxxstd-11-iso\threadapi-win32\threading-multi\visibility-hidden\codecvt_error_category.o  
In file included from ./boost/system/detail/append_int.hpp:8:0,  
                 from ./boost/system/detail/error_condition.hpp:18,  
                 from ./boost/system/detail/error_category_impl.hpp:14,  
                 from ./boost/system/error_category.hpp:11,  
                 from ./boost/filesystem/detail/path_traits.hpp:25,  
                 from libs/filesystem/src/codecvt_error_category.cpp:18:  
./boost/system/detail/snprintf.hpp:22:12: error: 'std::snprintf' has not been declared  
 using std::snprintf;  
            ^  
```  
  
```  
In file included from ./boost/system/detail/error_category_impl.hpp:79:0,  
                 from ./boost/system/error_category.hpp:11,  
                 from ./boost/filesystem/detail/path_traits.hpp:25,  
                 from libs/filesystem/src/codecvt_error_category.cpp:18:  
./boost/system/detail/mutex.hpp: At global scope:  
./boost/system/detail/mutex.hpp:84:12: error: 'std::mutex' has not been declared  
 using std::mutex;  
            ^  
```  
  
https://ci.appveyor.com/project/Lastique/filesystem/builds/48983852/job/2cgottx8b8ckwac0?fullLog=true#L376  
  
Is this platform no longer supported?

---

## Comment 1

> Username: Lastique  
> Created at: 2024-01-18 21:44:47 UTC  
> Url: https://github.com/boostorg/system/issues/116#issuecomment-1899249891  

BTW, there is `boost/core/snprintf.hpp` in Boost.Core with `snprintf` emulation.

---

## Comment 2

> Username: pdimov  
> Created at: 2024-01-18 23:24:49 UTC  
> Url: https://github.com/boostorg/system/issues/116#issuecomment-1899376533  

I suspect that nobody is using the plain MinGW (as opposed to mingw-w64) anymore, but I have no way to know for sure.  
  
For the time being I've removed (the old) MinGW from my Appveyor. We'll see if anyone (apart from you) notices.

---

## Comment 3

> Username: pdimov  
> Created at: 2024-01-18 23:25:38 UTC  
> Url: https://github.com/boostorg/system/issues/116#issuecomment-1899377311  

> BTW, there is `boost/core/snprintf.hpp` in Boost.Core with `snprintf` emulation.  
  
Yeah, there was an `snprintf.hpp` here too, before I removed it. :-)

---

## Comment 4

> Username: marakew  
> Created at: 2024-01-24 08:48:59 UTC  
> Updated at: 2024-01-24 08:52:25 UTC  
> Url: https://github.com/boostorg/system/issues/116#issuecomment-1907660291  

using msvc and C++20 the same issues with _snprintf  
  
```  
D:\T60\git\boost_\libs\system\include\boost/system/detail/snprintf.hpp(22): error C2039: '_snprintf': is not a member of 'std'  
D:\Program Files (x86)\Microsoft Visual Studio\2019\EnterprisePreview\VC\Tools\MSVC\14.29.30133\include\functional(31): note: see declaration of 'std'  
D:\T60\git\boost_\libs\system\include\boost/system/detail/snprintf.hpp(22): error C2873: '_snprintf': symbol cannot be used in a using-declaration  
D:\T60\git\boost_\libs\system\include\boost/system/detail/append_int.hpp(23): error C2039: '_snprintf': is not a member of 'boost::system::detail'  
D:\T60\git\boost_\libs\system\include\boost/system/detail/append_int.hpp(17): note: see declaration of 'boost::system::detail'  
D:\T60\git\boost_\libs\system\include\boost/system/detail/system_category_message_win32.hpp(33): error C2039: '_snprintf': is not a member of 'boost::system::detail'  
D:\T60\git\boost_\libs\system\include\boost/system/detail/system_category_message_win32.hpp(28): note: see declaration of 'boost::system::detail'  
D:\T60\git\boost_\libs\system\include\boost/system/detail/interop_category.hpp(58): error C2039: '_snprintf': is not a member of 'boost::system::detail'  
D:\T60\git\boost_\libs\system\include\boost/system/detail/interop_category.hpp(24): note: see declaration of 'boost::system::detail'  
D:\T60\git\boost_\libs\system\include\boost/system/detail/error_code.hpp(266): error C2039: '_snprintf': is not a member of 'boost::system::detail'  
D:\T60\git\boost_\libs\system\include\boost/system/detail/std_category.hpp(25): note: see declaration of 'boost::system::detail'  
D:\T60\git\boost_\libs\system\include\boost/system/detail/error_code.hpp(272): error C2039: '_snprintf': is not a member of 'boost::system::detail'  
D:\T60\git\boost_\libs\system\include\boost/system/detail/std_category.hpp(25): note: see declaration of 'boost::system::detail'  
D:\T60\git\boost_\libs\system\include\boost/system/detail/error_category_impl.hpp(61): error C2039: '_snprintf': is not a member of 'boost::system::detail'  
D:\T60\git\boost_\libs\system\include\boost/system/detail/std_category.hpp(25): note: see declaration of 'boost::system::detail'  
D:\T60\git\boost_\libs\system\include\boost/system/detail/error_category_impl.hpp(67): error C2039: '_snprintf': is not a member of 'boost::system::detail'  
D:\T60\git\boost_\libs\system\include\boost/system/detail/std_category.hpp(25): note: see declaration of 'boost::system::detail'  
  
```

---

## Comment 5

> Username: marakew  
> Created at: 2024-01-24 08:53:44 UTC  
> Url: https://github.com/boostorg/system/issues/116#issuecomment-1907668041  

look at there   
https://github.com/nlohmann/json/issues/1408#issuecomment-450559267

---

## Comment 6

> Username: Lastique  
> Created at: 2024-01-24 09:06:57 UTC  
> Updated at: 2024-02-06 20:58:45 UTC  
> Url: https://github.com/boostorg/system/issues/116#issuecomment-1907689149  

@marakew This looks like `snprintf` is defined as a macro somewhere and gets replaced to `_snprintf`. Boost.System doesn't do this, and I'm fairly sure MSVC standard library also doesn't, so you should review your library dependencies and report this to the developers of the offending library.  
  
In any case, this is not the issue that was reported here.

---

## Comment 7

> Username: marakew  
> Created at: 2024-01-24 09:43:31 UTC  
> Url: https://github.com/boostorg/system/issues/116#issuecomment-1907764829  

i think remove "using std::snprintf;" solve this  
  
there non sense use std::snprintf inside boost::system::detail

---

## Comment 8

> Username: pdimov  
> Created at: 2024-01-24 14:19:16 UTC  
> Url: https://github.com/boostorg/system/issues/116#issuecomment-1908224226  

```  
D:\T60\git\boost_\libs\system\include\boost/system/detail/snprintf.hpp(22): error C2039: '_snprintf': is not a member of 'std'  
```  
You have `#define snprintf _snprintf` somewhere. This is no longer needed on a recent MSVC (VS2015 and later) and needs to be removed.

---

## Comment 9

> Username: marakew  
> Created at: 2024-01-24 14:22:01 UTC  
> Url: https://github.com/boostorg/system/issues/116#issuecomment-1908229209  

drop "using std::snprintf" will be better solution

---

## Comment 10

> Username: Lastique  
> Created at: 2024-01-24 14:45:29 UTC  
> Url: https://github.com/boostorg/system/issues/116#issuecomment-1908275690  

> drop "using std::snprintf" will be better solution  
  
This is not a solution at all. Any `std::snprintf` or `boost::core::snprintf` calls will remain broken.

---

## Comment 11

> Username: marakew  
> Created at: 2024-01-24 14:56:43 UTC  
> Url: https://github.com/boostorg/system/issues/116#issuecomment-1908299403  

not  
and how this was work before commit mention above ?

---

## Comment 12

> Username: pdimov  
> Created at: 2024-01-24 14:59:51 UTC  
> Url: https://github.com/boostorg/system/issues/116#issuecomment-1908306268  

It will be more convenient for me because it will avoid bug reports such as yours, so I may well do that, but it's not a better solution overall, because you'll keep encountering these issues in other libraries until you fix your code to remove the #define.

---

## Comment 13

> Username: Lastique  
> Created at: 2024-01-24 15:10:04 UTC  
> Updated at: 2024-01-24 21:40:45 UTC  
> Url: https://github.com/boostorg/system/issues/116#issuecomment-1908326857  

> not and how this was work before commit mention above ?  
  
As long as you have the `define`, it didn't work and it won't work except by accident. Removing the `define` is the only solution.  
  
Note that `snprintf` and `_snprintf` are not equivalent. So the correctness of the `define` is questionable to begin with.
