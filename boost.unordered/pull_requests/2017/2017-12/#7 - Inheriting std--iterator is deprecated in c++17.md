# #7 Inheriting std::iterator is deprecated in c++17. [Merged]

> Username: DanielaE  
> Created at: 2017-12-28 11:06:31 UTC  
> Updated at: 2017-12-28 11:49:30 UTC  
> Merged at: 2017-12-28 11:31:12 UTC  
> Closed at: 2017-12-28 11:31:12 UTC  
> Url: https://github.com/boostorg/unordered/pull/7  

Therefore get rid of all of that and replace inheritance by lifting std::iterator's members into the derived class.  
  
Signed-off-by: Daniela Engert <dani@ngrt.de>

---
