# #148 Improve compatibility for recent MinGW-w64 and Cygwin compilers [Merged]

> Username: Flamefire  
> Created at: 2022-03-06 12:34:24 UTC  
> Updated at: 2022-03-07 11:10:26 UTC  
> Merged at: 2022-03-07 11:10:22 UTC  
> Closed at: 2022-03-07 11:10:22 UTC  
> Url: https://github.com/boostorg/nowide/pull/148  

On e.g. GCC 11 used in recent MinGW-w64 and Cygwin environments undefining `__STRICT_ANSI__` is no longer supported.  
  
However this is only required for the MinGW compilers, not the MinGW-w64 ones or on Cygwin.  
  
Fixes #146   
  
- Add CI for MSYS2 using a MinGW-w64 GCC 11 compiler reproducing #146  
- Add CI for more recent Cygwin to avoid regressions  
- Differentiate between MinGW and MinGW-w64 as the latter does not require the `__STRICT_ANSI__` handling  
- Improve `stat` function to detect differences in the struct definition between compiling and using the library  
- Enable the required extension functions on Cygwin via `_GNU_SOURCE`, not by touching `__STRICT_ANSI__`  
- Fix search paths for relative includes

---
