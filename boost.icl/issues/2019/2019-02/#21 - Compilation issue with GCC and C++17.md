# #21 - Compilation issue with GCC and C++17 [Open]

> Username: Timmmm  
> Created at: 2019-02-12 11:32:16 UTC  
> Updated at: 2019-02-12 11:32:16 UTC  
> Url: https://github.com/boostorg/icl/issues/21  

See https://bugzilla.redhat.com/show_bug.cgi?id=1485641  
  
TL;DR, there's a compilation error in ICL if you are using GCC with C++17. It works with Clang for me.  
  
A hacky workaround is to use `-fno-new-ttp-matching` when compiling with GCC.
