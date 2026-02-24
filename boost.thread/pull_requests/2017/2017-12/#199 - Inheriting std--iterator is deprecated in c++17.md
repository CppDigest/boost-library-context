# #199 Inheriting std::iterator is deprecated in c++17 [Merged]

> Username: DanielaE  
> Created at: 2017-12-29 16:33:06 UTC  
> Updated at: 2018-01-14 18:13:37 UTC  
> Merged at: 2017-12-30 00:48:35 UTC  
> Closed at: 2017-12-30 00:48:35 UTC  
> Url: https://github.com/boostorg/thread/pull/199  

Therefore get rid of that and replace inheritance by lifting std::iterator's members into the derived class.  
  
Signed-off-by: Daniela Engert <dani@ngrt.de>

---
