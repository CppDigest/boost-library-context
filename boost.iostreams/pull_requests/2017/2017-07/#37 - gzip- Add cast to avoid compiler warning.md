# #37 gzip: Add cast to avoid compiler warning. [Merged]

> Username: rdoeffinger  
> Created at: 2017-07-03 14:15:58 UTC  
> Updated at: 2017-07-03 14:51:34 UTC  
> Merged at: 2017-07-03 14:51:34 UTC  
> Closed at: 2017-07-03 14:51:34 UTC  
> Url: https://github.com/boostorg/iostreams/pull/37  

Avoids:  
warning: overflow in implicit constant conversion  
  
Ideally the casts should be completely avoided, since when char is  
signed, half of them including this one actually trigger undefined  
behaviour, i.e. this code is very broken, but there doesn't seem to  
be an easy fix for that.

---
