# #239 Fix string construction in native_environment_impl::get [Merged]

> Username: ivan-efimov  
> Created at: 2022-01-31 20:50:18 UTC  
> Updated at: 2023-02-06 12:33:13 UTC  
> Merged at: 2023-02-06 12:33:13 UTC  
> Closed at: 2023-02-06 12:33:13 UTC  
> Url: https://github.com/boostorg/process/pull/239  

Removing `+1` definitely resolves the issue in my case. Are there any possible cases when it doesn't?  
Closes #238

---

## Comment 1

> Username: klemens-morgenstern  
> Created_at: 2022-12-13 08:21:38 UTC  
> Url: https://github.com/boostorg/process/pull/239#issuecomment-1347913889  

Can you update the PR so the CI can run?  
  
Sorry it took so long for me to get to it.

---
