# #77 Make cstdint.hpp work on QNX 6.6 [Closed]

> Username: mauve  
> Created at: 2015-09-18 10:07:44 UTC  
> Updated at: 2015-09-24 17:17:18 UTC  
> Closed at: 2015-09-24 17:17:18 UTC  
> Url: https://github.com/boostorg/config/pull/77  

Before this commit we were using some QNX special extensions which have been deprecated, let's not use them unless QNX is built with extensions enabled.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2015-09-24 17:17:18 UTC  
> Url: https://github.com/boostorg/config/pull/77#issuecomment-142993116  

Merged to develop.

---
