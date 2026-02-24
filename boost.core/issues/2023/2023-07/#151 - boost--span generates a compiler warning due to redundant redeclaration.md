# #151 - boost::span generates a compiler warning due to redundant redeclaration [Closed]

> Username: hadrielk  
> Created at: 2023-07-22 18:57:22 UTC  
> Updated at: 2024-04-11 12:21:07 UTC  
> Closed at: 2024-04-11 12:21:07 UTC  
> Url: https://github.com/boostorg/core/issues/151  

When compiling `span.hpp`, `gcc 7.3` gives me this warning:  
  
```  
error: redundant redeclaration of 'constexpr' static data member 'boost::span<T, E>::extent' [-Werror=deprecated]  
```  
  
Because it's a `static constexpr` defined within the class, and then outside of it later in the file (as if for non-constexpr statics).

---

## Comment 1

> Username: pdimov  
> Created at: 2023-07-22 19:01:01 UTC  
> Url: https://github.com/boostorg/core/issues/151#issuecomment-1646651361  

That's odd because gcc 7.3 defaults to C++14 and the deprecation is C++17 if I'm not mistaken.

---

## Comment 2

> Username: hadrielk  
> Created at: 2023-07-22 19:14:29 UTC  
> Url: https://github.com/boostorg/core/issues/151#issuecomment-1646653559  

sorry, yes I'm compiling with `-std=c++17` - I should have mentioned that.

---

## Comment 3

> Username: pdimov  
> Created at: 2023-07-22 21:59:30 UTC  
> Url: https://github.com/boostorg/core/issues/151#issuecomment-1646679470  

Sounds like a variation of https://github.com/boostorg/system/pull/49 is in order. Would you be willing to submit a PR?

---

## Comment 4

> Username: hadrielk  
> Created at: 2023-07-23 01:50:07 UTC  
> Url: https://github.com/boostorg/core/issues/151#issuecomment-1646720401  

> Would you be willing to submit a PR?  
  
Sure. I'll submit one later this week.

---

## Comment 5

> Username: glenfe  
> Created at: 2024-04-11 12:21:07 UTC  
> Url: https://github.com/boostorg/core/issues/151#issuecomment-2049574731  

Addressed in 04cc766db315c9b573fa96aaa214c8acd8a16d64
