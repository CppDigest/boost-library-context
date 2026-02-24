# #183 CMake: fix detection of arm/aarch64 (#182) [Merged]

> Username: leha-bot  
> Created at: 2021-08-09 23:34:50 UTC  
> Updated at: 2021-08-14 15:50:06 UTC  
> Merged at: 2021-08-14 15:50:01 UTC  
> Closed at: 2021-08-14 15:50:01 UTC  
> Url: https://github.com/boostorg/context/pull/183  

Use CMAKE_SYSTEM_PROCESSOR at first step and if it matches to any  
Boost.Context arch option, then we will use it as default arch.  
Otherwise we try to detect the known arm names depending on current  
bitness.  
  
Fixes #182.

---

## Comment 1

> Username: olk  
> Created_at: 2021-08-10 07:39:39 UTC  
> Url: https://github.com/boostorg/context/pull/183#issuecomment-895803639  

Hi, could you modify your PR according to the MIPS architecture property (mips) - see commit:   
6edc8184 Switch mips1 to mips

---

## Comment 2

> Username: leha-bot  
> Created_at: 2021-08-10 11:58:43 UTC  
> Url: https://github.com/boostorg/context/pull/183#issuecomment-895968159  

Yes, added also all 32/64 bit MIPS-es. @FlyGoat sorry for mention, but could you run the CMake Boost build and check that the proper MIPS architecture is setting up?

---

## Comment 3

> Username: leha-bot  
> Created_at: 2021-08-10 12:26:17 UTC  
> Url: https://github.com/boostorg/context/pull/183#issuecomment-895985440  

seems that [this jam rule](https://github.com/boostorg/context/blob/561a31508f02889f9b18063156c4b019e9f827d7/build/Jamfile.v2#L77) should also be ported for proper work, will implement it too.

---

## Comment 4

> Username: olk  
> Created_at: 2021-08-14 15:50:05 UTC  
> Url: https://github.com/boostorg/context/pull/183#issuecomment-898910610  

ty

---
