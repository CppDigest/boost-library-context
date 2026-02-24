# #303 - MSVC Warning C4146 in dlmalloc_2_8_6.c and dlmalloc_ext_2_8_6.c [Closed]

> Username: vpatakottu  
> Created at: 2025-06-18 00:11:30 UTC  
> Updated at: 2025-09-06 10:23:01 UTC  
> Closed at: 2025-09-06 10:23:01 UTC  
> Url: https://github.com/boostorg/container/issues/303  

The following warnings are emitted when building boost container using the latest MSVC (Visual Studio 2022) compiler for x64 windows:  
  
```  
libs\container\src\dlmalloc_2_8_6.c(4294): warning C4146: unary minus operator applied to unsigned type, result still unsigned  
libs\container\src\dlmalloc_2_8_6.c(4432): warning C4146: unary minus operator applied to unsigned type, result still unsigned  
libs\container\src\dlmalloc_2_8_6.c(4460): warning C4146: unary minus operator applied to unsigned type, result still unsigned  
libs\container\src\dlmalloc_2_8_6.c(4463): warning C4146: unary minus operator applied to unsigned type, result still unsigned  
libs\container\src\dlmalloc_2_8_6.c(4505): warning C4146: unary minus operator applied to unsigned type, result still unsigned  
libs\container\src\dlmalloc_2_8_6.c(4596): warning C4146: unary minus operator applied to unsigned type, result still unsigned  
libs\container\src\dlmalloc_2_8_6.c(4597): warning C4146: unary minus operator applied to unsigned type, result still unsigned  
libs\container\src\dlmalloc_2_8_6.c(4624): warning C4146: unary minus operator applied to unsigned type, result still unsigned  
libs\container\src\dlmalloc_2_8_6.c(4895): warning C4146: unary minus operator applied to unsigned type, result still unsigned  
libs\container\src\dlmalloc_2_8_6.c(4917): warning C4146: unary minus operator applied to unsigned type, result still unsigned  
libs\container\src\dlmalloc_2_8_6.c(5186): warning C4146: unary minus operator applied to unsigned type, result still unsigned  
libs\container\src\dlmalloc_2_8_6.c(5229): warning C4146: unary minus operator applied to unsigned type, result still unsigned  
libs\container\src\dlmalloc_2_8_6.c(5273): warning C4146: unary minus operator applied to unsigned type, result still unsigned  
libs\container\src\dlmalloc_2_8_6.c(5423): warning C4146: unary minus operator applied to unsigned type, result still unsigned  
libs\container\src\dlmalloc_2_8_6.c(5443): warning C4146: unary minus operator applied to unsigned type, result still unsigned  
libs\container\src\dlmalloc_2_8_6.c(5530): warning C4146: unary minus operator applied to unsigned type, result still unsigned  
libs\container\src\dlmalloc_2_8_6.c(5531): warning C4146: unary minus operator applied to unsigned type, result still unsigned  
libs\container\src\dlmalloc_2_8_6.c(5558): warning C4146: unary minus operator applied to unsigned type, result still unsigned  
libs\container\src\dlmalloc_2_8_6.c(5736): warning C4146: unary minus operator applied to unsigned type, result still unsigned  
libs\container\src\dlmalloc_2_8_6.c(5779): warning C4146: unary minus operator applied to unsigned type, result still unsigned  
libs\container\src\dlmalloc_ext_2_8_6.c(209): warning C4146: unary minus operator applied to unsigned type, result still unsigned  
libs\container\src\dlmalloc_ext_2_8_6.c(210): warning C4146: unary minus operator applied to unsigned type, result still unsigned  
libs\container\src\dlmalloc_ext_2_8_6.c(237): warning C4146: unary minus operator applied to unsigned type, result still unsigned  
libs\container\src\dlmalloc_ext_2_8_6.c(728): warning C4146: unary minus operator applied to unsigned type, result still unsigned  
libs\container\src\dlmalloc_ext_2_8_6.c(728): warning C4146: unary minus operator applied to unsigned type, result still unsigned  
libs\container\src\dlmalloc_ext_2_8_6.c(1006): warning C4146: unary minus operator applied to unsigned type, result still unsigned  
libs\container\src\dlmalloc_ext_2_8_6.c(1014): warning C4146: unary minus operator applied to unsigned type, result still unsigned  
  
```  
## Environment  
Windows x64  
Compiler version: MSVC 19.44.35209.0  
  
## Repro Steps  
My organization requires addressing any suppressed warnings related to C4146 as it is considered a security-critical warning. These warnings can be seen here: https://godbolt.org/z/xPfe9Eqfc  
  
This warning is mainly found in the `dlmalloc_2_8_6.c` and `dlmalloc_ext_2_8_6.c` files. I wanted to report this in case it can be safely addressed. Would it be possible to resolve these warnings?

---

## Comment 1

> Username: DerekMeek  
> Created at: 2025-07-23 17:16:44 UTC  
> Url: https://github.com/boostorg/container/issues/303#issuecomment-3109476598  

my team also consumes this at the same organization and need this fixed.

---

## Comment 2

> Username: igaztanaga  
> Created at: 2025-08-25 21:33:17 UTC  
> Url: https://github.com/boostorg/container/issues/303#issuecomment-3221812152  

Thanks for the report, this was fixed in the https://github.com/boostorg/container/pull/307 pull.
