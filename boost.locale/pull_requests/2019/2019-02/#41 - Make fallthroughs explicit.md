# #41 Make fallthroughs explicit [Merged]

> Username: MarcelRaad  
> Created at: 2019-02-25 13:01:36 UTC  
> Updated at: 2022-05-28 11:42:47 UTC  
> Merged at: 2022-05-28 09:37:20 UTC  
> Closed at: 2022-05-28 09:37:20 UTC  
> Url: https://github.com/boostorg/locale/pull/41  

Use `BOOST_FALLTHROUGH` for annotating fallthroughs.  
This fixes clang and GCC warnings about implicit fallthrough.

---

## Comment 1

> Username: Kojoley  
> Created_at: 2019-04-19 16:01:06 UTC  
> Url: https://github.com/boostorg/locale/pull/41#issuecomment-484941673  

Ping

---

## Comment 2

> Username: Flamefire  
> Created_at: 2019-12-13 12:43:11 UTC  
> Url: https://github.com/boostorg/locale/pull/41#issuecomment-565428383  

@pdimov @artyom-beilis Could this be merged please? Or who is responsible for this repo now?

---

## Comment 3

> Username: EugeneZelenko  
> Created_at: 2020-04-11 17:47:03 UTC  
> Url: https://github.com/boostorg/locale/pull/41#issuecomment-612475503  

Similar warnings exist in libs/locale/src/util/gregorian.cpp. See #53.

---
