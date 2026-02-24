# #15 Inheriting std::iterator is deprecated in C++17 [Merged]

> Username: DanielaE  
> Created at: 2017-12-31 18:24:24 UTC  
> Updated at: 2018-01-14 16:26:09 UTC  
> Merged at: 2018-01-09 23:18:40 UTC  
> Closed at: 2018-01-09 23:18:40 UTC  
> Url: https://github.com/boostorg/ptr_container/pull/15  

Therefore replace inheritance by lifting std::iterator's members into the derived class.  
  
Signed-off-by: Daniela Engert <dani@ngrt.de>

---

## Comment 1

> Username: mclow  
> Created_at: 2017-12-31 20:05:22 UTC  
> Url: https://github.com/boostorg/ptr_container/pull/15#issuecomment-354621550  

I did some checks, and found no places where anyone is checking to see if these iterators are derived from `std::iterator`, so I think that this is a benign change.  
  
I'm a bit disturbed that there are no tests for these iterators (only tests for functions that create them), but this patch doesn't make that worse.

---
