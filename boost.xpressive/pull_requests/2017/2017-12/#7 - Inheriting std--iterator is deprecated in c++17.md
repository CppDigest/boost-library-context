# #7 Inheriting std::iterator is deprecated in c++17. [Merged]

> Username: DanielaE  
> Created at: 2017-12-28 09:16:47 UTC  
> Updated at: 2020-05-10 19:42:30 UTC  
> Merged at: 2020-05-10 19:42:29 UTC  
> Closed at: 2020-05-10 19:42:29 UTC  
> Url: https://github.com/boostorg/xpressive/pull/7  

Therefore get rid of all of that and replace inheritance by lifting std::iterator's members into the derived class.  
  
Signed-off-by: Daniela Engert <dani@ngrt.de>

---
