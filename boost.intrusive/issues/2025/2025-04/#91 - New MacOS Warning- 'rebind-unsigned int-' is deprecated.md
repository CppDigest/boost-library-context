# #91 - New MacOS Warning: 'rebind<unsigned int>' is deprecated [Closed]

> Username: taoduo  
> Created at: 2025-04-30 22:10:33 UTC  
> Updated at: 2025-05-01 19:58:46 UTC  
> Closed at: 2025-05-01 19:58:45 UTC  
> Url: https://github.com/boostorg/intrusive/issues/91  

MacOS starts to deprecate rebind for C++17, starting from the recent update to SDK15.4. Now boost packages are giving me a lot of warnings.  
I am using the most recent boost version 1.88.0.  
```  
pointer_rebind.hpp:81:35: warning: 'rebind<unsigned int>' is deprecated [-Wdeprecated-declarations]  
   81 |    typedef typename Ptr::template rebind<U>::other type;  
```  
  
Are we going to do something about these warnings?

---

## Comment 1

> Username: igaztanaga  
> Created at: 2025-05-01 19:58:45 UTC  
> Url: https://github.com/boostorg/intrusive/issues/91#issuecomment-2845639263  

pointer_rebind.hpp already includes "-Wdeprecated-declarations", we need more context of the error from this commit:  
  
https://github.com/boostorg/intrusive/commit/4ae39893292c77a67bb8fe9cd11683506c11e216  
  
This will be released with Boost 1.89
