# #52 Inheriting std::iterator is deprecated in c++17 [Closed]

> Username: DanielaE  
> Created at: 2017-12-31 16:46:06 UTC  
> Updated at: 2020-08-12 20:26:52 UTC  
> Closed at: 2020-08-12 20:26:52 UTC  
> Url: https://github.com/boostorg/ublas/pull/52  

Therefore replace inheritance by lifting std::iterator's members into the derived class.  
  
Signed-off-by: Daniela Engert <dani@ngrt.de>

---

## Comment 1

> Username: bassoy  
> Created_at: 2020-08-12 20:26:52 UTC  
> Url: https://github.com/boostorg/ublas/pull/52#issuecomment-673093454  

Thanks @DanielaE for the PR.  
The issue has been fixed with the PR https://github.com/boostorg/ublas/pull/97.

---
