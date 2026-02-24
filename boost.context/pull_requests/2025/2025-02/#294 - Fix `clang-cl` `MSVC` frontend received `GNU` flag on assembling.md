# #294 Fix `clang-cl` `MSVC` frontend received `GNU` flag on assembling [Merged]

> Username: Challanger524  
> Created at: 2025-02-22 16:50:15 UTC  
> Updated at: 2025-04-17 04:22:30 UTC  
> Merged at: 2025-04-17 04:22:19 UTC  
> Closed at: 2025-04-17 04:22:19 UTC  
> Url: https://github.com/boostorg/context/pull/294  

PR for #293  
  
Put `if (GNU/Clang)` into the MASM's `else` clause since `Clang-CL` validates positive at `CMAKE_CXX_COMPILER_ID STREQUAL "Clang"` while also having `MSVC` frontend for compile options and utilising `ML` masm assembler.  
  
UPD: `clang-cl` `/quiet` support has been dropped due to [inability to know real version for sure](https://github.com/boostorg/context/issues/293#issuecomment-2676320713).  
Side (weird) _fix_ for `/quiet` flag being passed on `MSVC_VERSION: 1936` despite Visual Studio Enterprise 2019 **Version 16.11.35**:  
  
<details><summary>Dropping context</summary>  
  
- Visual Studio Enterprise 2019 **Version 16.11.35**  
- (Microsoft (R) Macro Assembler (x64) Version 14.29.30154.0)  
- `message(STATUS "MSVC_VERSION: ${MSVC_VERSION}")` = `-- MSVC_VERSION: 1936` [report](https://github.com/boostorg/context/issues/293#issuecomment-2676274529)  
- despite [doc](https://learn.microsoft.com/en-us/cpp/assembler/masm/ml-and-ml64-command-line-reference?view=msvc-170#:~:text=Suppresses%20%27Assembling%27%20message.%20Available%20in%20Visual%20Studio%2017.6%20and%20later.) says that: `/quiet` Suppresses 'Assembling' message. Available in **Visual Studio 17.6** and later.  
Looks like CMake gives wrong `MSVC_VERSION` for `clang-cl`.  
  
</details>  
  
### Tested  
On MS Windows with CMake with next compilers: `MSVC`, msys2/mingw64/`GCC`, msys2/clang64/`Clang`, `Clang-CL` (chocolatey install)

---

## Comment 1

> Username: Challanger524  
> Created_at: 2025-02-22 16:57:24 UTC  
> Updated_at: 2025-02-22 16:57:42 UTC  
> Url: https://github.com/boostorg/context/pull/294#issuecomment-2676305688  

@marakew can you copy patch to your `boost::context` and validate?

---

## Comment 2

> Username: marakew  
> Created_at: 2025-02-22 17:06:48 UTC  
> Url: https://github.com/boostorg/context/pull/294#issuecomment-2676310028  

seems works

---

## Comment 3

> Username: marakew  
> Created_at: 2025-04-15 14:06:10 UTC  
> Url: https://github.com/boostorg/context/pull/294#issuecomment-2805235887  

@olk how about merge ?

---

## Comment 4

> Username: olk  
> Created_at: 2025-04-17 04:22:28 UTC  
> Url: https://github.com/boostorg/context/pull/294#issuecomment-2811698752  

ty

---
