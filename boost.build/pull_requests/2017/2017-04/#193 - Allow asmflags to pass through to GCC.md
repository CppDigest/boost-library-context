# #193 Allow asmflags to pass through to GCC [Merged]

> Username: mattyclarkson  
> Created at: 2017-04-25 15:48:22 UTC  
> Updated at: 2021-10-02 22:08:35 UTC  
> Merged at: 2017-09-07 19:15:18 UTC  
> Closed at: 2017-09-07 19:15:18 UTC  
> Url: https://github.com/boostorg/build/pull/193  

This is especially useful when cross-compiling Boost.Context for  
ARM32 as you need to pass the -mfpu=neon (or similar) to correctly  
compile the assembly there

---
