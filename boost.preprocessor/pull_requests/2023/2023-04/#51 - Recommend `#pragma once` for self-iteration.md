# #51 Recommend `#pragma once` for self-iteration [Open]

> Username: IncludeGuardian  
> Created at: 2023-04-22 17:03:03 UTC  
> Updated at: 2023-04-22 17:03:03 UTC  
> Url: https://github.com/boostorg/preprocessor/pull/51  

Compilers implement the "multiple-include optimization" for included files when they can detect that subsequent includes will have no effect. With Clang, GCC, and Microsoft Visual Studio this will occur when encountering a `#pragma once`, or when there is a **top-level** include guard.  
  
The idiom of self-inclusion with Boost Preprocessor requires that the include guard is nested within the `#if !BOOST_PP_IS_ITERATING` and therefore does not meet the strict criteria for the previously mentioned build optimization.  
  
The documentation has been updated to suggest `#pragma once` after all self-includes have occurred. This will allow the compiler to avoid any subsequent includes for the current translation unit and avoid extra work for the preprocessor.

---
