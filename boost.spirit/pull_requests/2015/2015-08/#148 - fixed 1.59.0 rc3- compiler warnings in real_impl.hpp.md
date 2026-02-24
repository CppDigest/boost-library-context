# #148 fixed 1.59.0 rc3: compiler warnings in real_impl.hpp [Closed]

> Username: tobias-loew  
> Created at: 2015-08-12 14:53:59 UTC  
> Updated at: 2017-12-13 21:20:09 UTC  
> Closed at: 2017-12-13 21:20:08 UTC  
> Url: https://github.com/boostorg/spirit/pull/148  

explicit type conversions & static_cast to avoid compiler  
warning  
warning C4244: '=' : conversion from 'unsigned __int64' to  
'double',  
possible loss of data  
in real_impl.hpp

---

## Comment 1

> Username: djowel  
> Created_at: 2015-08-13 04:54:28 UTC  
> Updated_at: 2015-08-13 04:55:09 UTC  
> Url: https://github.com/boostorg/spirit/pull/148#issuecomment-130535346  

Thanks! But can we have this PR against develop instead of master?

---

## Comment 2

> Username: tobias-loew  
> Created_at: 2015-08-13 07:38:55 UTC  
> Url: https://github.com/boostorg/spirit/pull/148#issuecomment-130565980  

I made a PR against develop but there the type_cast in line 72 is not included since both have type int. In master max_exp has type size_t which will result in a warning

---

## Comment 3

> Username: Kojoley  
> Created_at: 2017-12-13 21:20:08 UTC  
> Url: https://github.com/boostorg/spirit/pull/148#issuecomment-351528348  

Was replaced by #149

---
