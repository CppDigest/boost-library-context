# #124 - Add support for llvm based intel compilers [Open]

> Username: cpp977  
> Created at: 2022-04-29 08:22:52 UTC  
> Updated at: 2025-04-10 11:08:05 UTC  
> Url: https://github.com/boostorg/predef/issues/124  

The new llvm based intel compilers use a different preprocessor macro `__INTEL_LLVM_COMPILER` instead of `__INTEL_COMPILER`.   
This could be resolved by either adding a new compiler `BOOST_COMP_INTEL_LLVM` or by extending the current `BOOST_COMP_INTEL` part.  
See https://www.intel.com/content/www/us/en/develop/documentation/oneapi-dpcpp-cpp-compiler-dev-guide-and-reference/top/compiler-reference/macros/use-predefined-macros-for-intel-compilers.html for docs about the preprocessor macros by intel.

---

## Comment 1

> Username: dirtyharrycallahan  
> Created at: 2025-04-10 11:08:04 UTC  
> Url: https://github.com/boostorg/predef/issues/124#issuecomment-2792384963  

Too late for boost 1.88 but we have also hit this. Would you accept a pull request and if so do you have a preference for one of two options above?
