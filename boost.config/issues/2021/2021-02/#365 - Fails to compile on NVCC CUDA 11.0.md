# #365 - Fails to compile on NVCC CUDA 11.0 [Closed]

> Username: fmhess  
> Created at: 2021-02-05 16:45:16 UTC  
> Updated at: 2021-02-12 17:53:59 UTC  
> Closed at: 2021-02-12 17:53:59 UTC  
> Url: https://github.com/boostorg/config/issues/365  

I got a report that Boost.Signals2 fails to compile on NVCC:  
  
https://github.com/boostorg/signals2/issues/53  
  
Since, Signals2 doesn't directly check for CUDA, I assume the problem must be coming from Boost.Config.

---

## Comment 1

> Username: fmhess  
> Created at: 2021-02-12 17:53:59 UTC  
> Url: https://github.com/boostorg/config/issues/365#issuecomment-778346307  

This issue was resolved by a fix to Boost.Preprocessor
