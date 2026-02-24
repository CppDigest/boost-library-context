# #39 Reencoded a few headers that used Windows-1252 with UTF-8. [Merged]

> Username: Teemperor  
> Created at: 2017-06-02 12:54:50 UTC  
> Updated at: 2017-06-02 20:08:35 UTC  
> Merged at: 2017-06-02 20:08:35 UTC  
> Closed at: 2017-06-02 20:08:35 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/39  

Nearly every header in the boost codebase is UTF-8, but here there  
are a few headers which are using Windows-1252, which makes it impossible  
for some tools to parse those files. This patch just reencodes them  
with UTF-8 like the rest of the codebase. I checked that the name of the  
author is still correct after this change.  
  
No functional change intended.

---
