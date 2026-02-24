# #26 Switch boost::hash2::endian to std::endian when available [Closed]

> Username: HowardHinnant  
> Created at: 2024-12-03 04:35:40 UTC  
> Updated at: 2024-12-03 14:49:42 UTC  
> Closed at: 2024-12-03 14:49:42 UTC  
> Url: https://github.com/boostorg/hash2/pull/26  

Only very lightly tested.  But what if `boost::hash2::endian` becomes an alias for `std::endian` when `std::endian` is available?

---

## Comment 1

> Username: pdimov  
> Created_at: 2024-12-03 11:23:39 UTC  
> Url: https://github.com/boostorg/hash2/pull/26#issuecomment-2514275181  

I'm not a fan of things conditionally becoming std::things (except when implementation details) because this creates more (ODR) problems than it solves.

---

## Comment 2

> Username: HowardHinnant  
> Created_at: 2024-12-03 14:49:42 UTC  
> Url: https://github.com/boostorg/hash2/pull/26#issuecomment-2514784946  

Ok.

---
