# #186 - Buffer overflow in `floff` with `chars_format::fixed` [Closed]

> Username: mborland  
> Created at: 2024-04-19 06:59:45 UTC  
> Updated at: 2024-04-19 12:13:10 UTC  
> Closed at: 2024-04-19 12:13:10 UTC  
> Url: https://github.com/boostorg/charconv/issues/186  

There is a check for required buffer size here: https://github.com/boostorg/charconv/blob/develop/include/boost/charconv/detail/dragonbox/floff.hpp#L1556, but that's either not enough (since there's a comment to check again later for rounding), or incorrect.

---

## Comment 1

> Username: mborland  
> Created at: 2024-04-19 07:08:53 UTC  
> Url: https://github.com/boostorg/charconv/issues/186#issuecomment-2065896062  

Only occurs when precision is 0. Tested precisions -1 to 1000 work fine.
