# #288 Suppress build: `masm` logo and `clang` warning argument unused during compilation [Merged]

> Username: Challanger524  
> Created at: 2025-01-29 12:04:56 UTC  
> Updated at: 2025-04-16 08:51:55 UTC  
> Merged at: 2025-02-21 13:55:45 UTC  
> Closed at: 2025-02-21 13:55:45 UTC  
> Url: https://github.com/boostorg/context/pull/288  

PR for: https://github.com/boostorg/context/issues/286 and https://github.com/boostorg/context/issues/287  
  
## Concern  
  
These changes are applied to implementation `fcontext` (that is being set for me):  
> [build] -- Boost.Context: architecture x86_64, binary format pe, ABI ms, assembler gas, suffix .asm, implementation fcontext  
  
Maybe old code + new mine should affect other implementations (`ucontext`, `winfib`) too, maybe not - I don't know.  
  
## Further   
  
There still MASM output that informs/repeats the build file, looks like modern **VS 2022 17.7 Preview 1** is able to consume `/quiet` option for `ml` and `ml64`. But it is too modern :(  
https://gitlab.kitware.com/cmake/cmake/-/issues/23537#note_1316785  
  
/quiet | Suppresses 'Assembling' message. Available in Visual Studio 17.6 and later.  
-- | --  
  
https://learn.microsoft.com/en-us/cpp/assembler/masm/ml-and-ml64-command-line-reference?view=msvc-170  
  
### Version table  
  
https://learn.microsoft.com/en-us/cpp/overview/compiler-versions?view=msvc-170#version-macros  
  
|||  
-- | --  
Visual Studio 2022 version 17.5 | 1935  
Visual Studio 2022 version 17.6 | 1936  
Visual Studio 2022 version 17.7 | 1937  
  
### CMake - GREATER_EQUAL   
Added in version 3.7: Added the LESS_EQUAL and GREATER_EQUAL options.  
And context is 3.5: `if(NOT(MSVC_VERSION LESS 1936))` will it be  
https://stackoverflow.com/questions/16667017/cmake-express-the-greater-or-equal-statement  
  
### Concern  
It is better to check if we can pass `/quiet` on any 17.6 patch version, or better to set `1937` to be 100% sure that it works.

---

## Comment 1

> Username: olk  
> Created_at: 2025-02-21 13:55:51 UTC  
> Url: https://github.com/boostorg/context/pull/288#issuecomment-2674617774  

ty

---

## Comment 2

> Username: Challanger524  
> Created_at: 2025-02-21 15:34:02 UTC  
> Url: https://github.com/boostorg/context/pull/288#issuecomment-2674867454  

Just pulled updated **boost/context** with my PR fixes. It is so awesome to see those same fixes working without any local changes! Thank you for reviewing (hope it was reviewed) and somehow tested, since I don't see any CMake related CI/CD Actions.

---

## Comment 3

> Username: marakew  
> Created_at: 2025-02-21 20:18:33 UTC  
> Url: https://github.com/boostorg/context/pull/288#issuecomment-2675463311  

catch issue https://github.com/boostorg/context/issues/293

---

## Comment 4

> Username: Challanger524  
> Created_at: 2025-04-16 08:46:29 UTC  
> Updated_at: 2025-04-16 08:51:54 UTC  
> Url: https://github.com/boostorg/context/pull/288#issuecomment-2808849547  

https://github.com/boostorg/context/pull/294 PR fixing cmake logic for Clang-CL (MSVC frontend)

---
