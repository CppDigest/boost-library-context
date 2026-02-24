# #27 Fix boost.bind with libc++13 in -std=c++20 mode [Closed]

> Username: jcelerier  
> Created at: 2021-09-30 21:02:36 UTC  
> Updated at: 2021-10-01 09:35:41 UTC  
> Closed at: 2021-10-01 09:35:37 UTC  
> Url: https://github.com/boostorg/bind/pull/27  

The result_type member of std::less, etc. has been removed there to follow the standard

---

## Comment 1

> Username: pdimov  
> Created_at: 2021-10-01 00:31:29 UTC  
> Url: https://github.com/boostorg/bind/pull/27#issuecomment-931800447  

Should be fixed with https://github.com/boostorg/bind/commit/b719777942cf803cc1652d9865e7970bb0260296.

---

## Comment 2

> Username: jcelerier  
> Created_at: 2021-10-01 09:35:41 UTC  
> Url: https://github.com/boostorg/bind/pull/27#issuecomment-932075697  

great, thanks

---
