# #206 CI: Test multiple architectures [Merged]

> Username: Kojoley  
> Created at: 2022-09-19 02:26:13 UTC  
> Updated at: 2022-10-04 11:22:36 UTC  
> Merged at: 2022-10-04 04:57:54 UTC  
> Closed at: 2022-10-04 04:57:54 UTC  
> Url: https://github.com/boostorg/context/pull/206  

Run tests on most of supported platforms via QEMU. There seems to be no distro that supports powerpc, debian jessie packages for powerpc are no longer available.  
  
Some changes I had to make:  
- Run segmented stack tests only when compiler has `-fsplit-stack` and targeted libstdc++ (adds Clang, limits on unsupported arches and stdlibs).  
- Switch to lightweight tester from Boost.Test. It was failing mingw for some reason, and this also speed up qemu runners at least x2 (ppc64el 28mins vs 11mins).

---

## Comment 1

> Username: olk  
> Created_at: 2022-10-04 04:57:59 UTC  
> Url: https://github.com/boostorg/context/pull/206#issuecomment-1266398948  

ty

---
