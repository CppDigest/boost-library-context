# #57 Add more arch tags [Merged]

> Username: Neumann-A  
> Created at: 2024-04-19 21:11:55 UTC  
> Updated at: 2024-04-28 09:06:39 UTC  
> Merged at: 2024-04-20 14:17:11 UTC  
> Closed at: 2024-04-20 14:17:11 UTC  
> Url: https://github.com/boostorg/cmake/pull/57  

closes #53

---

## Comment 1

> Username: pdimov  
> Created_at: 2024-04-20 00:16:47 UTC  
> Url: https://github.com/boostorg/cmake/pull/57#issuecomment-2067416436  

`CMAKE_CXX_COMPILER_ARCHITECTURE_ID` is documented as "an internal variable subject to change" and there are StackOverflow reports that it's not always set (e.g. on MinGW-w64), so this has the potential to be a regression.  
  
E.g. https://gitlab.kitware.com/cmake/cmake/-/issues/17702

---

## Comment 2

> Username: pdimov  
> Created_at: 2024-04-20 00:39:05 UTC  
> Url: https://github.com/boostorg/cmake/pull/57#issuecomment-2067438856  

See also https://gitlab.kitware.com/cmake/cmake/-/issues/17701, https://gitlab.kitware.com/cmake/cmake/-/issues/18553, and https://gitlab.kitware.com/cmake/cmake/-/issues/15170.

---

## Comment 3

> Username: Neumann-A  
> Created_at: 2024-04-20 06:52:52 UTC  
> Url: https://github.com/boostorg/cmake/pull/57#issuecomment-2067580920  

I could also copy over what boost-context does   
https://github.com/boostorg/context/blob/f5d062c9399b6410cf1f90070bf6716f2ee0ced3/CMakeLists.txt#L32-L75  
and switch on `CMAKE_SYSTEM_PROCESSOR`.  
Alternatively guarding this code with `if(MSVC)` would also be possible.  
And of course provide an escape hatch and make  `BOOST_ARCHITECTURE_TAG` a `CACHE` variable.  
  
@pdimov: Any preferences?

---

## Comment 4

> Username: pdimov  
> Created_at: 2024-04-20 07:36:42 UTC  
> Url: https://github.com/boostorg/cmake/pull/57#issuecomment-2067591538  

The current state of CMake is that `CMAKE_CXX_COMPILER_ARCHITECTURE_ID` only works under MSVC, and `CMAKE_SYSTEM_PROCESSOR` only works when not MSVC. So if we want a solution that actually works, we need to use both.

---

## Comment 5

> Username: Neumann-A  
> Created_at: 2024-04-20 08:18:27 UTC  
> Url: https://github.com/boostorg/cmake/pull/57#issuecomment-2067600670  

>  CMAKE_SYSTEM_PROCESSOR only works when not MSVC  
  
`CMAKE_SYSTEM_PROCESSOR` always works. The issue you linked (https://gitlab.kitware.com/cmake/cmake/-/issues/15170) is a user error. If you do a cross build like x64 to x86 or arm you have to manually set `CMAKE_SYSTEM_PROCESSOR` because otherwise it will be set to `CMAKE_HOST_SYSTEM_PROCESSOR` as documented in the CMake docs. A lot of users are just unaware of this little fact.

---

## Comment 6

> Username: pdimov  
> Created_at: 2024-04-20 08:26:47 UTC  
> Url: https://github.com/boostorg/cmake/pull/57#issuecomment-2067602328  

No, that's not true. Read the comments on the issue again.

---

## Comment 7

> Username: Neumann-A  
> Created_at: 2024-04-20 08:55:34 UTC  
> Url: https://github.com/boostorg/cmake/pull/57#issuecomment-2067608549  

> No, that's not true. Read the comments on the issue again.  
  
I did and as a conclusion the docs for `CMAKE_SYSTEM_PROCESSOR` were updated. vcpkg even has this whole setup https://github.com/microsoft/vcpkg/blob/b346ee32f13f3bf2ef19ac02f982f6938f7a84ea/scripts/toolchains/windows.cmake#L8-L36 to setup cross builds on windows correctly from a CMake perspective. The problem is that people expect `CMAKE_SYSTEM_PROCESSOR` to be set correctly by CMake even though it as little to no info to do so.  
  
But ultimately I don't mind I can just add `if(MSVC)` do as FindBoost does and use the boost context logic otherwise.

---

## Comment 8

> Username: pdimov  
> Created_at: 2024-04-20 10:02:52 UTC  
> Url: https://github.com/boostorg/cmake/pull/57#issuecomment-2067624899  

Please make the following changes:  
  
* Remove `BOOST_ARCHITECTURE_TAG`  
* Use `arch` as the variable name instead of `__boost_default_arch_tag`  
* Remove the x86 cases and the warnings and always use "x" as a fallback  
* Fix the `string(APPEND` errors because the default value is "x"  
* Remove the IA64 case because I don't think anyone uses MSVC for IA64 anymore  
  
In principle we support more architectures (e.g. s390x, PowerPC) but I don't know what the `CMAKE_SYSTEM_PROCESSOR` correct values for them are.  
  
On second thought, I'm not sure MSVC supports MIPS anymore either. We can probably drop that case too.

---

## Comment 9

> Username: Neumann-A  
> Created_at: 2024-04-20 10:09:57 UTC  
> Url: https://github.com/boostorg/cmake/pull/57#issuecomment-2067626991  

strongly simplified as requested

---

## Comment 10

> Username: Neumann-A  
> Created_at: 2024-04-20 10:14:23 UTC  
> Url: https://github.com/boostorg/cmake/pull/57#issuecomment-2067628185  

> In principle we support more architectures (e.g. s390x, PowerPC) but I don't know what the CMAKE_SYSTEM_PROCESSOR correct values for them are.  
  
Where is the b2 logic for the selection for that? CMake is just using uname -m for the host I would assume b2 does something similar?

---

## Comment 11

> Username: pdimov  
> Created_at: 2024-04-20 11:49:36 UTC  
> Url: https://github.com/boostorg/cmake/pull/57#issuecomment-2067649779  

b2 uses configure checks and looks at predefined compiler macros: https://github.com/boostorg/config/tree/develop/checks/architecture

---

## Comment 12

> Username: pdimov  
> Created_at: 2024-04-20 12:29:42 UTC  
> Url: https://github.com/boostorg/cmake/pull/57#issuecomment-2067659055  

I'm going to merge this as it currently stands (after CI finishes), but here are some links if we return to this later:  
  
https://stackoverflow.com/questions/70475665/what-are-the-possible-values-of-cmake-system-processor  
https://stackoverflow.com/questions/45125516/possible-values-for-uname-m/45125525#45125525  
https://en.wikipedia.org/wiki/Uname

---

## Comment 13

> Username: Neumann-A  
> Created_at: 2024-04-20 13:07:40 UTC  
> Url: https://github.com/boostorg/cmake/pull/57#issuecomment-2067668354  

@pdimov: I also found those but the question is rather where the b2 build hides the mapping from architecture to character literal.

---

## Comment 14

> Username: pdimov  
> Created_at: 2024-04-20 14:03:34 UTC  
> Url: https://github.com/boostorg/cmake/pull/57#issuecomment-2067683976  

Character literal?

---

## Comment 15

> Username: Neumann-A  
> Created_at: 2024-04-20 14:10:30 UTC  
> Url: https://github.com/boostorg/cmake/pull/57#issuecomment-2067685918  

> Character literal?  
  
The mapping from "x64" to "x" or "aarch" to "a"

---

## Comment 16

> Username: pdimov  
> Created_at: 2024-04-20 14:13:20 UTC  
> Url: https://github.com/boostorg/cmake/pull/57#issuecomment-2067686708  

That's done by just taking the first letter of the architecture.

---

## Comment 17

> Username: Neumann-A  
> Created_at: 2024-04-20 21:32:16 UTC  
> Url: https://github.com/boostorg/cmake/pull/57#issuecomment-2067786877  

If I would have known that I probably would have used   
```  
      if(CMAKE_SYSTEM_PROCESSOR MATCHES "i(3|6)86")  
        set(arch "x")  
      else()  
        string(SUBSTRING "${CMAKE_SYSTEM_PROCESSOR}" 0 1 arch)  
        string(TOLOWER "${arch}" arch)  
      endif()  
```  
for the !MSVC part

---

## Comment 18

> Username: pdimov  
> Created_at: 2024-04-20 22:19:24 UTC  
> Url: https://github.com/boostorg/cmake/pull/57#issuecomment-2067802184  

I suppose that would work too; I can't offhand think of an architecture where it would give the wrong result.

---

## Comment 19

> Username: pdimov  
> Created_at: 2024-04-20 22:24:05 UTC  
> Url: https://github.com/boostorg/cmake/pull/57#issuecomment-2067802907  

`amd64`, maybe; Wikipedia lists this for OpenBSD 5.4.

---

## Comment 20

> Username: pdimov  
> Created_at: 2024-04-28 09:06:39 UTC  
> Url: https://github.com/boostorg/cmake/pull/57#issuecomment-2081401809  

Feel free to submit another PR for the above change to avoid the need to patch in vcpkg if you like - but please check for `amd64` in the x86 branch and maybe use `i[3-6]86` if someone still uses `i486`.

---
