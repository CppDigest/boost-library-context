# #281 - MSVC ARM64 build error. [Closed]

> Username: wyattoday  
> Created at: 2019-05-09 14:44:22 UTC  
> Updated at: 2019-05-10 11:48:32 UTC  
> Closed at: 2019-05-10 11:48:31 UTC  
> Url: https://github.com/boostorg/thread/issues/281  

When compiling the static libraries for ARM64 with vs2019, the build fails with the following error:  
  
```  
compile-c-c++ bin.v2\libs\thread\build\msvc-14.2\release\address-model-64\architecture-arm\link-static\runtime-link-static\threadapi-win32\threading-multi\win32\tss_pe.obj  
tss_pe.cpp  
libs\thread\src\win32\tss_pe.cpp(104): fatal error C1189: #error:  Unsupported platform  
  
    call "bin.v2\standalone\msvc\msvc-14.2\address-model-64\architecture-arm\msvc-setup.bat"  >nul  
 cl /Zm800 -nologo @"bin.v2\libs\thread\build\msvc-14.2\release\address-model-64\architecture-arm\link-static\runtime-link-static\threadapi-win32\threading-multi\win32\tss_pe.obj.rsp"  
  
...failed compile-c-c++ bin.v2\libs\thread\build\msvc-14.2\release\address-model-64\architecture-arm\link-static\runtime-link-static\threadapi-win32\threading-multi\win32\tss_pe.obj...  
```  
  
The fix is rather simple (it's a missing define). In `src/win32/tss_pe.cpp`, on line 101, change this:  
  
```  
#elif defined (_M_X64) || defined (_M_ARM)  
```  
  
To this:  
  
```  
#elif defined (_M_X64) || defined (_M_ARM) || defined (_M_ARM64)  
```  
  
  
So, that full #if-def block will look like this:  
  
  
```  
#if defined (_M_IX86)  
#pragma comment(linker, "/alternatename:__pRawDllMainOrig=__pDefaultRawDllMainOrig")  
#elif defined (_M_X64) || defined (_M_ARM) || defined (_M_ARM64)  
#pragma comment(linker, "/alternatename:_pRawDllMainOrig=_pDefaultRawDllMainOrig")  
#else  /* unknown Windows target (not x86, x64, ARM, ARM64) */  
#error Unsupported platform  
#endif  /* defined (_M_X64) || defined (_M_ARM) || defined (_M_ARM64) */  
```  
  
If you want I can make this is a pull request, but this is a simple enough change I figured one of the maintainers could make the 1-liner change.

---

## Comment 1

> Username: viboes  
> Created at: 2019-05-09 19:16:17 UTC  
> Url: https://github.com/boostorg/thread/issues/281#issuecomment-491031237  

Hi, thanks for the report.  
  
I would prefer a PR. Is this something you could do?

---

## Comment 2

> Username: wyattoday  
> Created at: 2019-05-09 19:20:55 UTC  
> Url: https://github.com/boostorg/thread/issues/281#issuecomment-491032566  

Sure, here's the fix: https://github.com/boostorg/thread/pull/282

---

## Comment 3

> Username: wyattoday  
> Created at: 2019-05-10 11:48:31 UTC  
> Url: https://github.com/boostorg/thread/issues/281#issuecomment-491261024  

Perfect, thanks!
