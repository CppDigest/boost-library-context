# #458 Remove broken 1.0#INF parser [Merged]

> Username: Kojoley  
> Created at: 2019-02-09 16:12:00 UTC  
> Updated at: 2019-02-10 06:50:07 UTC  
> Merged at: 2019-02-09 23:43:41 UTC  
> Closed at: 2019-02-09 23:43:41 UTC  
> Url: https://github.com/boostorg/spirit/pull/458  

It was poorly documented and never worked correctly. None of atof, strtof, and  
input streams are accepting such values. Fixing it would be a bigger breaking  
change than removing it.  
  
Fixes #415, addresses #163 and https://svn.boost.org/trac10/ticket/8699

---

## Comment 1

> Username: djowel  
> Created_at: 2019-02-10 06:50:07 UTC  
> Url: https://github.com/boostorg/spirit/pull/458#issuecomment-462108650  

Let's do it.

---
