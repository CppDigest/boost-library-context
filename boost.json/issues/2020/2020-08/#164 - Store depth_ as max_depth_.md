# #164 - Store depth_ as max_depth_ - [actual depth] [Closed]

> Username: sdkrystian  
> Created at: 2020-08-18 01:33:17 UTC  
> Updated at: 2020-08-20 02:21:56 UTC  
> Closed at: 2020-08-20 02:21:56 UTC  
> Url: https://github.com/boostorg/json/issues/164  

Using this method max depth is exceeded when `parse_array` or `parse_object` is called and `depth_ == 0`. After this check, we can safely decrement `depth_` without overflow.   
  
This eliminates a read from memory as we need not compare with `max_depth_`. We should probably specify what happens when depth is changed and the parser is suspended.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-08-18 01:55:05 UTC  
> Url: https://github.com/boostorg/json/issues/164#issuecomment-675203997  

calling `write_some` after changing the max depth is undefined. You have to call `reset` first.

---

## Comment 2

> Username: sdkrystian  
> Created at: 2020-08-18 02:03:39 UTC  
> Url: https://github.com/boostorg/json/issues/164#issuecomment-675206183  

Ah, we should document this then
