# #34 fix: 0-9 are valid characters for scheme [Closed]

> Username: swex  
> Created at: 2021-04-02 22:00:35 UTC  
> Updated at: 2021-09-08 20:10:26 UTC  
> Closed at: 2021-09-08 20:10:26 UTC  
> Url: https://github.com/boostorg/url/pull/34  



---

## Comment 1

> Username: vinniefalco  
> Created_at: 2021-04-02 23:29:14 UTC  
> Url: https://github.com/boostorg/url/pull/34#issuecomment-812753630  

hmm the first character can't be a digit >.<

---

## Comment 2

> Username: swex  
> Created_at: 2021-04-05 02:39:07 UTC  
> Url: https://github.com/boostorg/url/pull/34#issuecomment-813150186  

> hmm the first character can't be a digit >.<  
  
yeah, lib already checking for it in [parser.](https://github.com/CPPAlliance/url/blob/111b83cc4726f3144194dc3546e096d60ace317f/include/boost/url/detail/parse.hpp#L101)

---

## Comment 3

> Username: vinniefalco  
> Created_at: 2021-09-08 20:10:25 UTC  
> Url: https://github.com/boostorg/url/pull/34#issuecomment-915535872  

should work now

---
