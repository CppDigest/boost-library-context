# #164 - [clang-win] /Z7 should be passed to the compiler when variant=debug [Open]

> Username: hia3  
> Created at: 2017-02-17 13:24:07 UTC  
> Updated at: 2017-02-17 13:24:07 UTC  
> Url: https://github.com/boostorg/build/issues/164  

When compiling with toolset=msvc, BB passes /Z7 compiler option.  
  
But with toolset=clang-win this option is omitted. This prevents source-level debugging.
