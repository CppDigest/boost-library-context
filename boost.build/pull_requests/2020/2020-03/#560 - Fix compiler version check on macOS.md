# #560 Fix compiler version check on macOS [Merged]

> Username: Bo98  
> Created at: 2020-03-29 13:57:59 UTC  
> Updated at: 2021-10-02 21:13:22 UTC  
> Merged at: 2020-04-02 00:31:48 UTC  
> Closed at: 2020-04-02 00:31:48 UTC  
> Url: https://github.com/boostorg/build/pull/560  

Fixes #440.  
  
This fixes Xcode 11.4 builds. `gcc` is just an alias to `clang` in Xcode, but `-dumpversion` returned a fixed 4.2.1 for compatibility reasons (the last version of `gcc` Xcode shipped). Xcode 11.4 changed this to now return the Apple Clang version, which is now 11.0.3. Because the version checks were a lexicographical comparison, "11.0.3" was seen as < "4.0.0".

---
