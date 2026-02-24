# #138 Add header only consumption option [Closed]

> Username: mborland  
> Created at: 2024-01-24 09:20:34 UTC  
> Updated at: 2024-01-29 10:49:32 UTC  
> Closed at: 2024-01-29 10:49:32 UTC  
> Url: https://github.com/boostorg/charconv/pull/138  

Closes: #136

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2024-01-24 09:31:05 UTC  
> Url: https://github.com/boostorg/charconv/pull/138#issuecomment-1907735198  

An automated preview of the documentation is available at [https://138.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html](https://138.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html)

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2024-01-24 10:01:08 UTC  
> Url: https://github.com/boostorg/charconv/pull/138#issuecomment-1907796943  

An automated preview of the documentation is available at [https://138.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html](https://138.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html)

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2024-01-24 10:16:09 UTC  
> Url: https://github.com/boostorg/charconv/pull/138#issuecomment-1907824073  

An automated preview of the documentation is available at [https://138.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html](https://138.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html)

---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2024-01-24 10:51:24 UTC  
> Url: https://github.com/boostorg/charconv/pull/138#issuecomment-1907883698  

An automated preview of the documentation is available at [https://138.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html](https://138.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html)

---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2024-01-24 11:11:06 UTC  
> Url: https://github.com/boostorg/charconv/pull/138#issuecomment-1907916409  

An automated preview of the documentation is available at [https://138.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html](https://138.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html)

---

## Review 6 [Commented]

> Username: pdimov  
> Created_at: 2024-01-24 14:22:36 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/charconv/pull/138#pullrequestreview-1841497016  

📁 include/boost/charconv/config.hpp

```diff
  14 | 
  15 |- #if defined(BOOST_ALL_DYN_LINK) || defined(BOOST_CHARCONV_DYN_LINK)
  15 |+ #if defined(BOOST_ALL_DYN_LINK) || defined(BOOST_CHARCONV_DYN_LINK) && !defined(BOOST_CHARCONV_NO_LIB)
```

> Username: pdimov  
> Created_at: 2024-01-24 14:22:36 UTC  
> Url: https://github.com/boostorg/charconv/pull/138#discussion_r1464987449  

This change isn't correct.


---

## Comment 7

> Username: cppalliance-bot  
> Created_at: 2024-01-25 10:06:05 UTC  
> Url: https://github.com/boostorg/charconv/pull/138#issuecomment-1909804793  

An automated preview of the documentation is available at [https://138.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html](https://138.charconv.prtest.cppalliance.org/libs/charconv/doc/html/index.html)

---
