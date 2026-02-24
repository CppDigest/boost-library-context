# #33 Fix compilation in case if key is void*, again [Merged]

> Username: Lastique  
> Created at: 2018-12-14 16:04:28 UTC  
> Updated at: 2019-01-03 13:35:44 UTC  
> Merged at: 2019-01-03 00:18:02 UTC  
> Closed at: 2019-01-03 00:18:02 UTC  
> Url: https://github.com/boostorg/intrusive/pull/33  

This PR fixes the compilation error in case if the user specified a key of type `void*`. The problem was previously fixed in https://github.com/boostorg/intrusive/pull/21 but reappeared in 1.69.  
  
The PR also adds a test to verify the fix.  
  
Additionally, it converts some tabs to spaces in iterator.hpp.

---

## Comment 1

> Username: igaztanaga  
> Created_at: 2019-01-03 00:19:51 UTC  
> Url: https://github.com/boostorg/intrusive/pull/33#issuecomment-451025749  

Many thanks.

---
