# #130 Reencoded a few headers that used Windows-1252 with UTF-8. [Merged]

> Username: Teemperor  
> Created at: 2017-06-02 13:01:50 UTC  
> Updated at: 2017-06-02 21:44:47 UTC  
> Merged at: 2017-06-02 21:44:47 UTC  
> Closed at: 2017-06-02 21:44:47 UTC  
> Url: https://github.com/boostorg/python/pull/130  

Nearly every header in the boost codebase is UTF-8, but here there  
are a few headers which are using Windows-1252, which makes it impossible  
for some tools to parse those files. This patch just reencodes them  
with UTF-8 like the rest of the codebase. I checked that the name of the  
author is still correct after this change.  
  
No functional change intended.

---

## Comment 1

> Username: stefanseefeld  
> Created_at: 2017-06-02 16:48:37 UTC  
> Url: https://github.com/boostorg/python/pull/130#issuecomment-305847708  

This looks good; thanks !  
I'm waiting for travis-ci results, though (which seems particularly slow today).

---
