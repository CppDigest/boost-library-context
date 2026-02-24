# #319 CMakeLists: improve detection of PowerPC architectures [Merged]

> Username: erikbs  
> Created at: 2025-10-29 02:39:56 UTC  
> Updated at: 2025-10-29 20:21:08 UTC  
> Merged at: 2025-10-29 20:21:03 UTC  
> Closed at: 2025-10-29 20:21:03 UTC  
> Url: https://github.com/boostorg/context/pull/319  

We’re building Boost for ppc32 and switching from b2 to CMake, but the CMake-based system defaults to using i386 asm files. A similar issue has been reported for ppc64le: #311   
  
I noticed that in the list of default architectures there is “power”, but not “ppc32” or “ppc64”, which are the architecture names found in the actual assembly file names. I therefore replaced “power” with “ppc32 ppc64” and “ppc32, ppc64” in the lists of architectures, which does the trick when CMAKE_SYSTEM_PROCESSOR is set to “ppc32” or “ppc64”. However, I’ve also seen simply “powerpc” being used, and there is also “ppc64le” etc., so I added two additional checks, one for each bitness, so that “powerpc” and values beginning with “ppc64” are detected as ppc64 on 64-bit and “powerpc” and values beginning with “ppc32” are detected as “ppc32” on 32-bit.  
  
When searching for usages, I did find code checking for just “power”, but I deliberately did not catch that with my code, since IBM POWER and PowerPC are separate architectures. I’m not sure if there are real-world use cases where CMAKE_SYSTEM_PROCESSOR is just “power” (our toolchain sets it to “powerpc”, but “ppc32” would probably be more correct)

---

## Comment 1

> Username: olk  
> Created_at: 2025-10-29 20:21:08 UTC  
> Url: https://github.com/boostorg/context/pull/319#issuecomment-3463769958  

ty

---
