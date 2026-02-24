# #233 - cstdint.hpp is lacking SIZE_MAX [Closed]

> Username: jzmaddock  
> Created at: 2018-07-31 18:28:44 UTC  
> Updated at: 2018-08-04 18:22:26 UTC  
> Closed at: 2018-08-04 18:22:26 UTC  
> Url: https://github.com/boostorg/config/issues/233  

Header cstdint.hpp is lacking the constant SIZE_MAX that is normally defined in stdint.h.  
  
Since cstdint.hpp is supposed to be a portable C++ port of the standard C99 header stdint.h, I believe that missing constant should be added.  
  
Moved from Trac

---

## Comment 1

> Username: jzmaddock  
> Created at: 2018-08-04 17:23:58 UTC  
> Url: https://github.com/boostorg/config/issues/233#issuecomment-410464704  

We're missing:  
  
```  
  
PTRDIFF_MIN(C99)         minimum value of object of ptrdiff_t type  
PTRDIFF_MAX(C99)        maximum value of object of ptrdiff_t type  
SIZE_MAX(C99)              maximum value of object of size_t type  
SIG_ATOMIC_MIN(C99)  minimum value of object of sig_atomic_t type  
SIG_ATOMIC_MAX(C99)  maximum value of object of sig_atomic_t type  
WINT_MIN(C99)              minimum value of object of wint_t type  
WINT_MAX(C99)             maximum value of object of wint_t type  
  
```  
  
But I see no obvious way of implementing these as macros.

---

## Comment 2

> Username: jzmaddock  
> Created at: 2018-08-04 18:22:26 UTC  
> Url: https://github.com/boostorg/config/issues/233#issuecomment-410468289  

On reflection, closing as not fixable - or which is to say - please supply a PR.
