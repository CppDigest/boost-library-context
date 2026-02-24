# #431 Set coverage flags for gcc and clang: [Merged]

> Username: djarek  
> Created at: 2019-04-30 15:45:32 UTC  
> Updated at: 2021-10-02 21:19:01 UTC  
> Merged at: 2019-04-30 16:13:24 UTC  
> Closed at: 2019-04-30 16:13:24 UTC  
> Url: https://github.com/boostorg/build/pull/431  

- `<coverage>all` sets up `--coverage` for compiler and linker  
- `<coverage>profile` sets up `-fprofile-arcs` for compiler and linker  
- `<coverage>test` sets up `-ftest-coverage` for compiler and  
  `-lgcov` for linker  
  
Reference:  
https://gcc.gnu.org/onlinedocs/gcc/Instrumentation-Options.html

---
