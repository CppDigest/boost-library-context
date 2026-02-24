# #12 Reencoded a few headers that used Windows-1252 with UTF-8. [Merged]

> Username: Teemperor  
> Created at: 2017-06-02 12:48:54 UTC  
> Updated at: 2017-06-15 02:21:25 UTC  
> Merged at: 2017-06-15 02:21:25 UTC  
> Closed at: 2017-06-15 02:21:25 UTC  
> Url: https://github.com/boostorg/interval/pull/12  

Nearly every header in the boost codebase is UTF-8, but here there  
are a few headers which are using Windows-1252, which makes it impossible  
for some tools to parse those files. This patch just reencodes them  
with UTF-8 like the rest of the codebase. I checked that the name of the  
author is still correct after this change and the reencoded string  
literal was the plus-minus-sign.  
  
No functional change intended.

---
