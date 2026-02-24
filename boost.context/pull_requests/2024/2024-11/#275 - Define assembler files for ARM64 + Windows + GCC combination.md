# #275 Define assembler files for ARM64 + Windows + GCC combination [Merged]

> Username: vejbomar  
> Created at: 2024-11-11 10:07:53 UTC  
> Updated at: 2024-12-26 07:12:48 UTC  
> Merged at: 2024-12-26 07:12:42 UTC  
> Closed at: 2024-12-26 07:12:42 UTC  
> Url: https://github.com/boostorg/context/pull/275  

Adds a definition which assembler files should be used for ARM64 + Windows + GCC combination. (This is needed for a new target `aarch64-w64-mingw32` for MinGW which is being developed here: https://github.com/Windows-on-ARM-Experiments/mingw-woarm64-build)  
  
It is enough to use the existing assembler for Clang on Arm64 (introduced here https://github.com/boostorg/context/pull/262) as they both use the GNU syntax.  
  
Also the first commit fixes the assembler extensions for Clang.

---

## Comment 1

> Username: eukarpov  
> Created_at: 2024-11-19 08:42:29 UTC  
> Url: https://github.com/boostorg/context/pull/275#issuecomment-2485048214  

@olk Could you please review this change?

---

## Comment 2

> Username: olk  
> Created_at: 2024-12-26 07:12:47 UTC  
> Url: https://github.com/boostorg/context/pull/275#issuecomment-2562240238  

ty

---
