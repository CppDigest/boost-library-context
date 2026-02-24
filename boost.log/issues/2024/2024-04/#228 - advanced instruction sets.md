# #228 - advanced instruction sets [Closed]

> Username: tobias-loew  
> Created at: 2024-04-13 12:06:35 UTC  
> Updated at: 2024-04-13 14:00:33 UTC  
> Closed at: 2024-04-13 13:59:18 UTC  
> Url: https://github.com/boostorg/log/issues/228  

Hi,  
I'm building the boost libraries on a Windows Server 2019 with a Intel Core i5 with AVX512 processor using msvc143.  
The libraries are linked into a Windows desktop app, which is run on all kind of machines, sometimes with old processors not supporting AVX or SSSE3. (Only SSE2 is assumed.)  
The problem is that the jam-files automatically detect that AVX is supported on my build machine and the log-libraries are built with "/arch:AVX" enabled and "BOOST_LOG_USE_AVX2" defined.  
  
The question is: does BOOST_LOG_USE_AVX2 (resp. BOOST_LOG_USE_SSSE3) require that AVX/SSE3 is present, or does it just introduce faster alternative code paths?  
  
If AVX/SSE3 is required, then how to build Boost.Log without such instructions on a machine that support them?

---

## Comment 1

> Username: Lastique  
> Created at: 2024-04-13 13:59:18 UTC  
> Updated at: 2024-04-13 14:00:33 UTC  
> Url: https://github.com/boostorg/log/issues/228#issuecomment-2053654479  

SSSE3 and AVX2 code paths are optional and enabled based on runtime CPU detection. The output you see during compilation is about whether the _compiler_ supports intrinsics for these instruction sets. The extra compiler flags are only applied to source files containing SSSE3 and AVX2 code paths. The code should run on CPUs without these ISA extensions just fine.
