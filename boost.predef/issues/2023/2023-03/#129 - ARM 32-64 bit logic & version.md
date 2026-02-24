# #129 - ARM 32/64 bit logic & version? [Open]

> Username: jcelerier  
> Created at: 2023-03-30 20:52:08 UTC  
> Updated at: 2023-04-02 04:57:01 UTC  
> Url: https://github.com/boostorg/predef/issues/129  

The ARM macros define the version to 8 if `__aarch64__` is defined.   
But I believe this is not correct: ARMv8 can run in aarch32 mode, where only `__arm__` and not `__aarch64__` is defined (and conversely, AArch64 mode does *not* define `__arm__`).  
  
The correct thing I believe would be to use `__ARM_ARCH` which expands to e.g. 7 in ARMv7, 8 in ARMv8 etc for the version, and use the existence of `__arm__` / `__aarch64__` to distinguish between 32  & 64.  
  
see repro: https://gcc.godbolt.org/z/xqqYWEaz6   
  
  
ideally I would argue that AArch64 ought to be treated as an entirely different architecture, like PPC / PPC64
