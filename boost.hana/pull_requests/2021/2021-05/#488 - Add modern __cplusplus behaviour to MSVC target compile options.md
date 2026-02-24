# #488 Add modern __cplusplus behaviour to MSVC target compile options [Closed]

> Username: JamesJCode  
> Created at: 2021-05-30 08:25:40 UTC  
> Updated at: 2021-05-30 11:55:31 UTC  
> Closed at: 2021-05-30 11:27:37 UTC  
> Url: https://github.com/boostorg/hana/pull/488  

Addition to further fix #475 without end-user CMake intervention  
  
Adds /Zc:__cplusplus to MSVC compile options (as a `hana` target `INTERFACE` parameter) to force correct C++ standard reporting in `__cplusplus` variable.

---

## Comment 1

> Username: JamesJCode  
> Created_at: 2021-05-30 11:27:37 UTC  
> Url: https://github.com/boostorg/hana/pull/488#issuecomment-850984197  

Close for more fine-grained solution

---
