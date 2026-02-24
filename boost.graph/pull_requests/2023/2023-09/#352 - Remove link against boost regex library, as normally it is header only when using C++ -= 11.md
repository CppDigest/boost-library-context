# #352 Remove link against boost regex library, as normally it is header only when using C++ >= 11. [Merged]

> Username: Romain-Geissler-1A  
> Created at: 2023-09-25 16:36:27 UTC  
> Updated at: 2024-01-01 03:50:40 UTC  
> Merged at: 2024-01-01 03:50:25 UTC  
> Closed at: 2024-01-01 03:50:25 UTC  
> Url: https://github.com/boostorg/graph/pull/352  

It seems you recently dropped all support for gcc 4.x and C++ < 11 in general, so normally with the compilers you still support the Boost regex library shall be header only. Thus avoid linking against it explicitly.  
  
I didn't test this locally, so let's wait the result of the CI.

---

## Comment 1

> Username: Romain-Geissler-1A  
> Created_at: 2023-09-25 17:08:25 UTC  
> Url: https://github.com/boostorg/graph/pull/352#issuecomment-1734150115  

It looks like this breaks all clang < 4.x tests. How ok is it to remove clang 3.6 to 3.9 from the supported compilers ? Clang 4.0.0 debuted in march 2017, so 6 years ago. The oldest gcc release you still support, gcc 5, debuted in April 2015, so 8 years ago.

---

## Comment 2

> Username: jeremy-murphy  
> Created_at: 2023-09-26 23:58:44 UTC  
> Url: https://github.com/boostorg/graph/pull/352#issuecomment-1736457236  

Thanks for updating this.  
  
I'd like to remove more old compilers from the CI, but I'll just check my notes to see if anyone was actively using them.

---

## Comment 3

> Username: jeremy-murphy  
> Created_at: 2023-12-07 05:17:00 UTC  
> Url: https://github.com/boostorg/graph/pull/352#issuecomment-1844459187  

I just removed Clang 3.x from the Drone config, so please rebase on to the latest `develop`.

---

## Comment 4

> Username: Romain-Geissler-1A  
> Created_at: 2023-12-07 12:01:12 UTC  
> Url: https://github.com/boostorg/graph/pull/352#issuecomment-1845218904  

Ok I just did, let's see how the CI reacts now ;)

---

## Comment 5

> Username: jeremy-murphy  
> Created_at: 2023-12-07 23:18:06 UTC  
> Url: https://github.com/boostorg/graph/pull/352#issuecomment-1846253076  

Thanks, that's great. The change looks good to me, but I'm not an expert on the b2 build configuration, so I might ask someone else to quickly review it too.

---

## Comment 6

> Username: pdimov  
> Created_at: 2023-12-07 23:49:28 UTC  
> Url: https://github.com/boostorg/graph/pull/352#issuecomment-1846278398  

It doesn't matter whether a library is header-only or not, you still have to link to it, or it won't work.  
  
If this doesn't break CI, then CI doesn't test CMake.

---

## Comment 7

> Username: Romain-Geissler-1A  
> Created_at: 2023-12-08 00:01:16 UTC  
> Url: https://github.com/boostorg/graph/pull/352#issuecomment-1846288913  

Ok, I have reverted the CMakeLists.txt changes.

---

## Comment 8

> Username: Romain-Geissler-1A  
> Created_at: 2023-12-22 17:54:42 UTC  
> Url: https://github.com/boostorg/graph/pull/352#issuecomment-1867940709  

Ping

---

## Comment 9

> Username: jeremy-murphy  
> Created_at: 2024-01-01 03:50:06 UTC  
> Url: https://github.com/boostorg/graph/pull/352#issuecomment-1873135892  

> Ping  
  
Sorry, been on holidays!

---

## Comment 10

> Username: jeremy-murphy  
> Created_at: 2024-01-01 03:50:39 UTC  
> Url: https://github.com/boostorg/graph/pull/352#issuecomment-1873136054  

Thanks for improving that!

---
