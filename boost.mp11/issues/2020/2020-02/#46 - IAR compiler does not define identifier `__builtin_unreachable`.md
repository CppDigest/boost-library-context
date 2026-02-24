# #46 - IAR compiler does not define identifier `__builtin_unreachable` [Closed]

> Username: jeremy-murphy  
> Created at: 2020-02-28 01:04:45 UTC  
> Updated at: 2020-03-02 02:40:47 UTC  
> Closed at: 2020-03-01 10:00:31 UTC  
> Url: https://github.com/boostorg/mp11/issues/46  

When I tried to compile Variant2 1.72.0, I hit this error with IAR compiler for Arm 8.30.2:  
  
`Error[Pe020]: identifier "__builtin_unreachable" is undefined C:\Src\boost_1_72_0\boost\mp11\detail\mp_with_index.hpp 83 `  
  
and on all the other lines where `__builtin_unreachable` is used.

---

## Comment 1

> Username: pdimov  
> Created at: 2020-03-01 10:01:09 UTC  
> Url: https://github.com/boostorg/mp11/issues/46#issuecomment-593078007  

Should be fixed on develop with https://github.com/boostorg/mp11/commit/1f634c7071d38aff82578efa745a65e54dca4fb3.

---

## Comment 2

> Username: jeremy-murphy  
> Created at: 2020-03-02 02:40:47 UTC  
> Url: https://github.com/boostorg/mp11/issues/46#issuecomment-593192905  

Thanks!
