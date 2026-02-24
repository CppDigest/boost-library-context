# #279 Refactor range [Merged]

> Username: vinniefalco  
> Created at: 2022-07-30 23:32:04 UTC  
> Updated at: 2022-08-02 17:43:31 UTC  
> Merged at: 2022-08-01 01:20:52 UTC  
> Closed at: 2022-08-01 01:20:52 UTC  
> Url: https://github.com/boostorg/url/pull/279  

Instead of awkward function pointers we just store the begin and increment rules directly. Furthermore, if the small buffer size is insufficient, a dynamic allocation is performed. This allocation is based on the size of the rules, not the size of the range. None of the rules in the library currently exceed the small buffer size.

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2022-07-30 23:39:48 UTC  
> Url: https://github.com/boostorg/url/pull/279#issuecomment-1200313468  

An automated preview of the documentation is available at [https://279.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://279.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2022-07-31 15:18:04 UTC  
> Url: https://github.com/boostorg/url/pull/279#issuecomment-1200444937  

An automated preview of the documentation is available at [https://279.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://279.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2022-07-31 20:08:05 UTC  
> Url: https://github.com/boostorg/url/pull/279#issuecomment-1200491032  

An automated preview of the documentation is available at [https://279.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://279.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2022-08-01 01:09:45 UTC  
> Url: https://github.com/boostorg/url/pull/279#issuecomment-1200563411  

An automated preview of the documentation is available at [https://279.url.prtest.cppalliance.org/libs/url/doc/html/index.html](https://279.url.prtest.cppalliance.org/libs/url/doc/html/index.html)

---
