# #472 Fix homebrew include path. [Merged]

> Username: stefanseefeld  
> Created at: 2025-02-01 18:29:59 UTC  
> Updated at: 2025-02-02 03:55:25 UTC  
> Merged at: 2025-02-02 03:55:02 UTC  
> Closed at: 2025-02-02 03:55:02 UTC  
> Url: https://github.com/boostorg/python/pull/472  



---

## Comment 1

> Username: pdimov  
> Created_at: 2025-02-01 18:44:01 UTC  
> Url: https://github.com/boostorg/python/pull/472#issuecomment-2629065433  

This seems to have worked, but the build now fails for a different reason, Optional requires C++11. I see that the command line passes `cxxflags=-std=c++11`, but then the log says  
```  
configuration check results:  
  has_cxx11 : False   
  has_numpy : False   
```  
so it looks like this doesn't take for some reason.  
  
Maybe `faber` should be modified to print the command line on failure, like `b2` does.  
  
The problem could be that the `makedep` rule doesn't apply `cxxflags`.

---

## Comment 2

> Username: stefanseefeld  
> Created_at: 2025-02-02 03:55:07 UTC  
> Updated_at: 2025-02-02 03:55:25 UTC  
> Url: https://github.com/boostorg/python/pull/472#issuecomment-2629229834  

Good catch @pdimov . Indeed, `faber` uses the `cppflags` feature to configure the pre-processor, and `cxxflags` for the (C++) compiler. Setting the `-std` option in both has fixed the build problem. Thanks !

---
