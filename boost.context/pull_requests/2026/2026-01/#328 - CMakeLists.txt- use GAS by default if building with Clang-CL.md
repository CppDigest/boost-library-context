# #328 CMakeLists.txt: use GAS by default if building with Clang-CL [Open]

> Username: erikbs  
> Created at: 2026-01-31 03:33:54 UTC  
> Updated at: 2026-01-31 03:33:54 UTC  
> Url: https://github.com/boostorg/context/pull/328  

`if (MSVC)` evaluates to true if building with Clang-CL, but at least if cross-compiling with Clang-CL from a non-Windows host, MASM (default for MSVC) is not understood. Using GAS, however, works perfectly. I therefore propose to restrict this check to trigger only with MSVC and not Clang-CL. I have not tested this extensively, but it works in my case where I use Clang-CL on a GNU/Linux host targeting Windows.  
  
Of course another option is to override the detected assembler using `-DBOOST_CONTEXT_ASSEMBLER="gas"`, but automatic detection makes build scripts a bit cleaner.

---
