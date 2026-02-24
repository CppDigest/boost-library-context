# #125 - Please add make_span [Closed]

> Username: HDembinski  
> Created at: 2022-09-17 15:24:34 UTC  
> Updated at: 2023-01-28 21:20:43 UTC  
> Closed at: 2023-01-28 21:20:43 UTC  
> Url: https://github.com/boostorg/core/issues/125  

span.hpp follows the standard implementation, which does not have make_span, since make_span is superfluous for C++17 onwards. It is very useful to use span with lower C++ standards. Can this please be added?

---

## Comment 1

> Username: glenfe  
> Created at: 2022-09-18 17:36:17 UTC  
> Url: https://github.com/boostorg/core/issues/125#issuecomment-1250353869  

Will do.

---

## Comment 2

> Username: HDembinski  
> Created at: 2022-09-19 11:45:19 UTC  
> Url: https://github.com/boostorg/core/issues/125#issuecomment-1250911066  

https://github.com/boostorg/histogram/blob/develop/include/boost/histogram/detail/span.hpp#L252

---

## Comment 3

> Username: glenfe  
> Created at: 2023-01-28 21:20:43 UTC  
> Url: https://github.com/boostorg/core/issues/125#issuecomment-1407490700  

Also available on develop now
