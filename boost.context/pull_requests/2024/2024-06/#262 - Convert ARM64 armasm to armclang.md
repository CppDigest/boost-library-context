# #262 Convert ARM64 armasm to armclang [Merged]

> Username: Liryna  
> Created at: 2024-06-18 15:13:22 UTC  
> Updated at: 2024-08-28 10:39:21 UTC  
> Merged at: 2024-08-28 10:39:21 UTC  
> Closed at: 2024-08-28 10:39:21 UTC  
> Url: https://github.com/boostorg/context/pull/262  

The armasm legacy assembler is deprecated. The code was migrated to the GNU syntax (used by armclang).  
https://developer.arm.com/documentation/100068/0621/Migrating-from-armasm-to-the-armclang-Integrated-Assembler  
  
Split this into two commit to keep the history and diff clean

---

## Comment 1

> Username: dixyes  
> Created_at: 2024-06-19 01:26:56 UTC  
> Updated_at: 2024-06-19 01:27:07 UTC  
> Url: https://github.com/boostorg/context/pull/262#issuecomment-2177350030  

Does Microsoft provide armclang in the default installation of Visual Studio?  
  
These codes are used in many projects. If armasm support is removed, they may require users to install additional dependencies (and a build system modification).

---

## Comment 2

> Username: dixyes  
> Created_at: 2024-06-19 01:53:08 UTC  
> Url: https://github.com/boostorg/context/pull/262#issuecomment-2177373148  

BTW the armasm in file name means [ms armasm64](https://learn.microsoft.com/en-us/cpp/assembler/arm/arm-assembler-command-line-reference?view=msvc-170), not ARM armasm, they are not deprecated yet.  
  
So I think keeping these armasm64 sources, adding new armclang sources/b2 configurations then wait for ms providing armclang is better

---

## Comment 3

> Username: Liryna  
> Created_at: 2024-06-19 02:15:49 UTC  
> Url: https://github.com/boostorg/context/pull/262#issuecomment-2177408785  

Correct, I couldn't find msvc support armclang for now 😢   
Therefore I kept the existing one and added the converted armclang only

---
