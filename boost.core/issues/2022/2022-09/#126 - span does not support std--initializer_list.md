# #126 - span does not support std::initializer_list [Closed]

> Username: HDembinski  
> Created at: 2022-09-17 15:41:08 UTC  
> Updated at: 2023-02-18 16:54:44 UTC  
> Closed at: 2023-01-28 21:18:36 UTC  
> Url: https://github.com/boostorg/core/issues/126  

https://godbolt.org/z/be94ra9G3  
```cpp  
#include <span>  
#include <boost/core/span.hpp>  
#include <initializer_list>  
  
int main() {  
    auto x = {1, 2, 3};  
    auto sp = std::span(x); // works  
    auto sp2 = boost::span(x); // fails  
}  
```  
In Boost Histogram, where I am trying to switch to boost/core/span.hpp now, I am seeing errors like this:  
`error: no viable conversion from 'const std::initializer_list<X>' to 'span<const X>'`. So it is fundamentally not an issue with the deduction guides.  
  
The old span that I had implemented internally for histogram supported this.

---

## Comment 1

> Username: HDembinski  
> Created at: 2022-09-19 11:43:21 UTC  
> Url: https://github.com/boostorg/core/issues/126#issuecomment-1250909234  

I think the span is missing something like this: https://github.com/boostorg/histogram/blob/develop/include/boost/histogram/detail/nonmember_container_access.hpp  
  
and a corresponding ctor like this:  
  
https://github.com/boostorg/histogram/blob/develop/include/boost/histogram/detail/span.hpp#L139

---

## Comment 2

> Username: HDembinski  
> Created at: 2022-12-23 00:24:48 UTC  
> Updated at: 2022-12-23 00:25:05 UTC  
> Url: https://github.com/boostorg/core/issues/126#issuecomment-1363465793  

@glenfe Any progress on this and #125? I would like to move Histogram over to this span and drop my internal implementation, but I cannot until at least std::initializer_list is supported. I can add make_span myself.

---

## Comment 3

> Username: glenfe  
> Created at: 2023-01-18 19:43:52 UTC  
> Url: https://github.com/boostorg/core/issues/126#issuecomment-1387672713  

Sorry for the delay @HDembinski. I'll finish both up this month, they'll be done for 1.82.0

---

## Comment 4

> Username: HDembinski  
> Created at: 2023-01-19 07:47:50 UTC  
> Url: https://github.com/boostorg/core/issues/126#issuecomment-1396562670  

Awesome, thanks!

---

## Comment 5

> Username: glenfe  
> Created at: 2023-01-28 21:18:36 UTC  
> Url: https://github.com/boostorg/core/issues/126#issuecomment-1407490373  

Should be implemented on develop. Let me know if there are any issues using it in Histogram.

---

## Comment 6

> Username: HDembinski  
> Created at: 2023-01-30 08:23:58 UTC  
> Url: https://github.com/boostorg/core/issues/126#issuecomment-1408173803  

Thanks! Will do.

---

## Comment 7

> Username: glenfe  
> Created at: 2023-02-18 13:27:55 UTC  
> Url: https://github.com/boostorg/core/issues/126#issuecomment-1435669691  

@HDembinski  I've merged to master; I was waiting to see if you needed more functionality (e.g. support for `std::valarray`) but there's still time to get that in.

---

## Comment 8

> Username: HDembinski  
> Created at: 2023-02-18 16:54:44 UTC  
> Url: https://github.com/boostorg/core/issues/126#issuecomment-1435715998  

Works perfectly thanks.
