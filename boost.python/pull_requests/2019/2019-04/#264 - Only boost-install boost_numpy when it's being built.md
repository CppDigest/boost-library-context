# #264 Only boost-install boost_numpy when it's being built [Merged]

> Username: pdimov  
> Created at: 2019-04-17 21:06:58 UTC  
> Updated at: 2019-04-17 22:08:16 UTC  
> Merged at: 2019-04-17 22:08:16 UTC  
> Closed at: 2019-04-17 22:08:16 UTC  
> Url: https://github.com/boostorg/python/pull/264  

The library `boost_numpy` is conditioned on `[ python.numpy ]`, so only `boost-install` it when this condition holds.  
  
This also moves the libraries outside `if [ python.configured ]` (as was the case before) since they are already guarded with `[ cond ]`.

---

## Comment 1

> Username: stefanseefeld  
> Created_at: 2019-04-17 21:10:12 UTC  
> Url: https://github.com/boostorg/python/pull/264#issuecomment-484262763  

Thanks ! (Feel free to merge this right away without waiting for CI builds to finish. They don't use `b2`, so wouldn't be able to detect any issues anyhow. :-)

---
