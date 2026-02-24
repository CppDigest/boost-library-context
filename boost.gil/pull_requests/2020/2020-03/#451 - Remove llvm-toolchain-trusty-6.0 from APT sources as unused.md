# #451 Remove llvm-toolchain-trusty-6.0 from APT sources as unused [Merged]

> Username: mloskot  
> Created at: 2020-03-15 16:44:18 UTC  
> Updated at: 2020-03-15 20:15:02 UTC  
> Merged at: 2020-03-15 20:14:58 UTC  
> Closed at: 2020-03-15 20:14:58 UTC  
> Url: https://github.com/boostorg/gil/pull/451  

This just fixes CI infrastructure issue, APT conflict.  
We no longer need to allow `VARIANT=gil_ubsan_integer` job to fail.  
  
### Tasklist  
  
<!-- Add YOUR OWN TASK(s), especially if your PR is a work in progress -->  
  
- [x] Ensure all CI builds pass

---
