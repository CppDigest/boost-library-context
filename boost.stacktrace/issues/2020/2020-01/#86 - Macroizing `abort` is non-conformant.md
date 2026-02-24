# #86 - Macroizing `abort` is non-conformant [Closed]

> Username: StephanTLavavej  
> Created at: 2020-01-06 23:46:24 UTC  
> Updated at: 2020-01-22 20:30:17 UTC  
> Closed at: 2020-01-22 20:30:16 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/86  

This code is non-conformant and fails to compile with the current development version of Visual Studio:  
  
https://github.com/boostorg/stacktrace/blob/211d29125310fa14c38d8a38cb7ef1cbca863928/example/assert_handler.cpp#L27-L34  
  
First, implementing `ignore_abort` in `namespace std` is forbidden by [N4842](https://wg21.link/N4842) 16.5.4.2.1 [namespace.std]/1: "Unless otherwise specified, the behavior of a C++ program is undefined if it adds declarations or definitions to namespace `std` or to a namespace within namespace `std`."  
  
Second, macroizing `abort` is extremely forbidden by 16.5.4.3.2 [macro.names]/1: "A translation unit that includes a standard library header shall not `#define` or `#undef` names declared in any standard library header."  
  
Third, this is failing to compile for the current development version of MSVC's STL because including `<iostream>` drags in `<istream>`, `<ostream>`, `<ios>`, internal `<xlocnum>`, and finally `<cmath>` where our implementation of `lerp()` contains a call to `::abort()` which is broken by the macroized `abort`: https://github.com/microsoft/STL/blob/94c9f9965b4e39bab9fdec6f71f1fad320e96d8b/stl/inc/cmath#L1379-L1380  
  
The `assert_handler.cpp` example should be changed to follow the Standard's requirements.

---

## Comment 1

> Username: apolukhin  
> Created at: 2020-01-22 20:30:16 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/86#issuecomment-577371677  

Fixed in 178d287, merged to master.  
  
Sorry for the mess
