# #130 - Testing 1.76.0 (I was using 1.71.0 before) and now some windows.h macros spills out [Closed]

> Username: vricosti  
> Created at: 2021-06-03 09:17:02 UTC  
> Updated at: 2021-10-06 12:44:23 UTC  
> Closed at: 2021-10-06 12:44:23 UTC  
> Url: https://github.com/boostorg/regex/issues/130  

Hi,  
  
I wanted to update my code by using a recent boost version 1.76.0  instead of 1.71.0 and now one of my method named CreateFile does not compile because it is replaced by a Windows macro from fileapi.h that replace CreateFile with CreateFileW ...  
When I preprocess the source file I can see that fileapi is included because WIndows.h is included as show below:  
  
#line 29 "D:\\Stage\\Boost-1.76.0-msvc15-x86_64\\Debug\\include\\boost/regex/v5/w32_regex_traits.hpp"  
#line 1 "C:\\Program Files (x86)\\Windows Kits\\10\\Include\\10.0.18362.0\\um\\windows.h"  
  
Maybe it would be interesting to at least undef some of these macros if it's not possible to avoid that...  
CreateFile, DeleteFile are pretty common names to use for a method

---

## Comment 1

> Username: jzmaddock  
> Created at: 2021-06-06 08:03:14 UTC  
> Url: https://github.com/boostorg/regex/issues/130#issuecomment-855357319  

Nod.  I was aware that this might be a potential issue, but decided that being able to go header only was worth it.  
  
Unfortunately, messing with the contents of windows.h would break things for folks who need that #include, so I don't see a good fix right now.

---

## Comment 2

> Username: rconde01  
> Created at: 2021-06-08 18:33:30 UTC  
> Url: https://github.com/boostorg/regex/issues/130#issuecomment-857000613  

I think you can at least change w32_regex_traits.hpp to:  
```  
#if !defined(VC_EXTRALEAN)  
#define VC_EXTRALEAN  
#endif  
  
#if !defined(WIN32_LEAN_AND_MEAN)  
#define WIN32_LEAN_AND_MEAN  
#endif  
```  
to fix macro redefinition warnings

---

## Comment 3

> Username: jzmaddock  
> Created at: 2021-06-08 18:35:17 UTC  
> Url: https://github.com/boostorg/regex/issues/130#issuecomment-857001608  

Done in: https://github.com/boostorg/regex/commit/dc9c5c50be88b0340152ed98fbab5ef5605bebc2

---

## Comment 4

> Username: jzmaddock  
> Created at: 2021-10-06 12:44:23 UTC  
> Url: https://github.com/boostorg/regex/issues/130#issuecomment-936166356  

This has now been addressed in develop
