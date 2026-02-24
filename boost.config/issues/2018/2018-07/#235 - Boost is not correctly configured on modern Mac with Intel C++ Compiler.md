# #235 - Boost is not correctly configured on modern Mac with Intel C++ Compiler [Open]

> Username: jzmaddock  
> Created at: 2018-07-31 18:44:05 UTC  
> Updated at: 2018-07-31 18:44:05 UTC  
> Url: https://github.com/boostorg/config/issues/235  

Boost does not work properly on modern macOS, for example 10.11, with Intel C++ Compiler.  
  
When compiling with default language/standard/compatibility flags, select_compiler_config.hpp picks up intel.hpp which does not define BOOST_NO_RTTI and BOOST_NO_RTTI due to __INTEL_RTTI__. This is wrong, since Intel C++ 2016 builds upon LLVM/Clang which does not have RTTI. As a result, if your application uses RTTI-dependent operators (like typeid()), it breaks.  
  
When you compile with the following flags:  
  
-no-use-intel-optimized-headers -no-intel-extensions -no-icc -no-gcc  
  
...then Boost picks up clang.hpp. But the Clang version handling in this header file is broken since Apple has different versioning scheme for Clang.  
  
However, if you use the flag -clangxx-name=<clang> with a custom Clang version, then the Clang versioning scheme is back to normal.  
  
When building with Intel C++ on macOS, both Clang and Intel compiler specific settings need to be considered.  
  
Moved from Trac.
