# #694 Autodetect the clang-win default address-model from the -v output [Merged]

> Username: pdimov  
> Created at: 2020-12-28 13:53:45 UTC  
> Updated at: 2021-10-02 20:59:45 UTC  
> Merged at: 2020-12-29 16:08:18 UTC  
> Closed at: 2020-12-29 16:08:18 UTC  
> Url: https://github.com/boostorg/build/pull/694  

For the 64 bit installation of LLVM, `clang-cl -v` outputs  
```  
clang version 11.0.0  
Target: x86_64-pc-windows-msvc  
Thread model: posix  
InstalledDir: C:\LLVM\bin  
```  
so this looks for `Target: x86_64` and if so, uses 64 for the default, instead of always using 32 as before.

---

## Comment 1

> Username: github-actions[bot]  
> Created_at: 2021-10-02 20:59:44 UTC  
> Url: https://github.com/boostorg/build/pull/694#issuecomment-932819690  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2 Please consider following up at https://github.com/bfgroup/b2/pulls

---
