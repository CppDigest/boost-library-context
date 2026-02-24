# #40 fix compilation for nvcc+clang [Merged]

> Username: BenjaminW3  
> Created at: 2017-08-28 06:13:21 UTC  
> Updated at: 2017-08-28 13:27:22 UTC  
> Merged at: 2017-08-28 07:51:22 UTC  
> Closed at: 2017-08-28 07:51:22 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/40  

nvcc seems to use the host compiler for preprocessing the source for the device and host compilation.  
When compiling the host code with the host compiler (clang), `__builtin_assume` is detected correctly and is also available during compilation.  
When compiling the device code with nvcc, this builtin function is not provided by nvcc.

---

## Comment 1

> Username: BenjaminW3  
> Created_at: 2017-08-28 13:27:22 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/40#issuecomment-325352960  

@pdimov Could this fix please be added to the upcoming boost-1.65.1 release see [here](http://boost.2283326.n4.nabble.com/release-Starting-1-65-1-td4698309.html)?

---
