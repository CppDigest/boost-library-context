# #53 Fix register usage for ppc64/sysv/elfv2 [Merged]

> Username: DaoWen  
> Created at: 2017-04-18 22:07:39 UTC  
> Updated at: 2017-04-22 14:14:02 UTC  
> Merged at: 2017-04-22 14:14:02 UTC  
> Closed at: 2017-04-22 14:14:02 UTC  
> Url: https://github.com/boostorg/context/pull/53  

I was able to set up some PowerPC virtual machines using QEMU, and I tested the previous patch on ppc (32-bit), ppc64 big-endian (ELFv1), and ppc64 little-endian (ELFv2). The previous patch didn't work with ELFv2 (significant ABI differences from ELFv1). This version was tested and passes the unit tests on all three of the virtual machines mentioned above, although I was only able to run the fcontext test on the 32-bit ppc target since I was using an older version of GCC.

---

## Comment 1

> Username: olk  
> Created_at: 2017-04-22 14:13:56 UTC  
> Url: https://github.com/boostorg/context/pull/53#issuecomment-296375950  

that's very nice, ty

---
