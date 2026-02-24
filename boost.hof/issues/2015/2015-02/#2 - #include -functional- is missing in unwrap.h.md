# #2 - #include <functional> is missing in unwrap.h [Closed]

> Username: bwoods  
> Created at: 2015-02-14 22:30:21 UTC  
> Updated at: 2015-02-15 06:06:10 UTC  
> Closed at: 2015-02-15 06:06:10 UTC  
> Url: https://github.com/boostorg/hof/issues/2  

Needed for the [`std::reference_wrapper`](http://en.cppreference.com/w/cpp/utility/functional/reference_wrapper) used by `unwrap_reference`.   
  
> **Compiler**:  
> Apple LLVM version 6.0 (clang-600.0.56) (based on LLVM 3.5svn)  
> Target: x86_64-apple-darwin14.1.0  
> Thread model: posix

---

## Comment 1

> Username: pfultz2  
> Created at: 2015-02-15 06:06:10 UTC  
> Url: https://github.com/boostorg/hof/issues/2#issuecomment-74405594  

Added header, thanks.
