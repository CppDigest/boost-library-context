# #149 fixed 1.59.0 rc3: compiler warnings in real_impl.hpp [Closed]

> Username: tobias-loew  
> Created at: 2015-08-13 07:42:09 UTC  
> Updated at: 2017-12-13 21:19:51 UTC  
> Closed at: 2017-12-13 21:19:50 UTC  
> Url: https://github.com/boostorg/spirit/pull/149  

explicit type conversions & static_cast to avoid compiler  
warning  
warning C4244: '=' : conversion from 'unsigned __int64' to  
'double',  
possible loss of data  
in real_impl.hpp

---

## Comment 1

> Username: tobias-loew  
> Created_at: 2015-08-13 07:43:56 UTC  
> Url: https://github.com/boostorg/spirit/pull/149#issuecomment-130566605  

type_cast in line 72 is not included since both have type int. In master-branch max_exp has type size_t which will result in a compiler-warning

---

## Comment 2

> Username: Kojoley  
> Created_at: 2017-12-13 21:19:50 UTC  
> Url: https://github.com/boostorg/spirit/pull/149#issuecomment-351528270  

Was replaced by #151

---
