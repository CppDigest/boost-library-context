# #471 - macOS CI failures [Open]

> Username: pdimov  
> Created at: 2025-02-01 17:43:47 UTC  
> Updated at: 2025-02-01 18:34:54 UTC  
> Url: https://github.com/boostorg/python/issues/471  

The macOS CI fails because of the hardcoded path to Boost here  
  
```  
faber \  
    --with-boost-include=/opt/homebrew/Cellar/boost/1.86.0/include \  
```  
  
This should probably be `$(brew --prefix boost)/include`.

---

## Comment 1

> Username: stefanseefeld  
> Created at: 2025-02-01 18:34:52 UTC  
> Url: https://github.com/boostorg/python/issues/471#issuecomment-2629062365  

Thanks @pdimov , I'm following up on this with https://github.com/boostorg/python/pull/472
