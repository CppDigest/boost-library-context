# #526 - `GLIBC_2.28' not found: Regression between 1.74.0 and 1.75.0? [Closed]

> Username: elsamuko  
> Created at: 2021-06-15 19:28:20 UTC  
> Updated at: 2021-06-21 14:56:10 UTC  
> Closed at: 2021-06-21 14:56:10 UTC  
> Url: https://github.com/boostorg/boost/issues/526  

Hello,  
  
after I build my project [fsrc](https://github.com/elsamuko/fsrc) on Ubuntu **20.04.** with boost 1.75 I'm getting the error message  
```  
"/lib/x86_64-linux-gnu/libc.so.6: version `GLIBC_2.28'  
not found"  
```  
when running the binary on Ubuntu **18.04.** despite having linked `libstdc++` and `libgcc` statically. With 1.74 everything works fine.

---

## Comment 1

> Username: elsamuko  
> Created at: 2021-06-21 14:56:10 UTC  
> Url: https://github.com/boostorg/boost/issues/526#issuecomment-865099943  

This issue was caused by the statx syscall used by `boost::filesystem::exists()`:    
* https://github.com/boostorg/filesystem/issues/172  
* https://github.com/boostorg/filesystem/issues/173  
* https://github.com/boostorg/filesystem/issues/182
