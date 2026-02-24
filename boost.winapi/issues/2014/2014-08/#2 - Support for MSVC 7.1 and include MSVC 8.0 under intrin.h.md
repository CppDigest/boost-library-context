# #2 - Support for MSVC 7.1 and include MSVC 8.0 under intrin.h [Closed]

> Username: igaztanaga  
> Created at: 2014-08-22 21:37:16 UTC  
> Updated at: 2014-08-23 15:24:31 UTC  
> Closed at: 2014-08-23 15:24:31 UTC  
> Url: https://github.com/boostorg/winapi/issues/2  

Interprocess in MSVC 7.1 does not link in debug (intrinsics are not enabled in Debug mode). This happened after support for BOOST_USE_INTRIN_H was added. This patch adds support for Visual 2003 using #pragma intrinsic and also uses intrin.h for Visual 2005 (_MSC_VER == 1400).  
  
Interprocess correctly compiles after this patch is applied:  
  
https://gist.github.com/anonymous/f826fb6e82c2bbcbef62

---

## Comment 1

> Username: Lastique  
> Created at: 2014-08-23 15:24:31 UTC  
> Url: https://github.com/boostorg/winapi/issues/2#issuecomment-53155888  

Fixed in e7c3a828a92d223f00955f5070cd0ec81e9176dd.
