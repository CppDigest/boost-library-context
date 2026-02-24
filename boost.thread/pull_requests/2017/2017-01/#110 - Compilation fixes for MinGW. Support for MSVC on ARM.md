# #110 Compilation fixes for MinGW. Support for MSVC on ARM. [Merged]

> Username: Lastique  
> Created at: 2017-01-07 17:26:33 UTC  
> Updated at: 2017-01-22 12:47:41 UTC  
> Merged at: 2017-01-22 09:19:54 UTC  
> Closed at: 2017-01-22 09:19:54 UTC  
> Url: https://github.com/boostorg/thread/pull/110  

1. Apparently, some versions of MinGW declare interlocked intrinsics  
without volatile qualifiers. To avoid compilation failures cast away  
volatile from pointers. This should also work with qualified pointers as  
well.  
  
2. Changed interlocked read/write helpers for MSVC. VS2012 and later have  
the option to change volatile behavior to the standard C++. In that mode,  
volatile no longer enforces memory ordering. To ensure the correct  
behavior of the helpers, use compiler barriers to prohibit code  
reordering. Also, limit the version to x86 only.  
  
3. Added a separate version of interlocked read/write helpers for MSVC on  
ARM. To avoid dependency on the compiler switch, use intrinsics to load  
and store values and also emit the hardware memory fence.  
  
4. Added two versions of interlocked read/write helpers for gcc and  
compatible compilers. One version is based on __atomic intrinsics, the  
other one (for older versions) uses inline assembler. These versions  
should be more optimal than the generic fallback.

---

## Comment 1

> Username: viboes  
> Created_at: 2017-01-22 09:18:28 UTC  
> Url: https://github.com/boostorg/thread/pull/110#issuecomment-274319222  

Thanks Andrey for working on this.

---
