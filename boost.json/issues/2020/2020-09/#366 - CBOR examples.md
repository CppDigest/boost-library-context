# #366 - CBOR examples [Closed]

> Username: vinniefalco  
> Created at: 2020-09-19 18:29:52 UTC  
> Updated at: 2025-01-14 09:21:42 UTC  
> Closed at: 2025-01-14 09:21:42 UTC  
> Url: https://github.com/boostorg/json/issues/366  

We could take Peter's CBOR code from the review and make it an example

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-10-09 01:46:59 UTC  
> Url: https://github.com/boostorg/json/issues/366#issuecomment-705919066  

https://github.com/pdimov/boost_json_review

---

## Comment 2

> Username: grisumbras  
> Created at: 2022-05-25 10:24:52 UTC  
> Url: https://github.com/boostorg/json/issues/366#issuecomment-1137067717  

That code uses Boost.Endian. If we're going to test it, we'll have to add dependency on Endian in CMake. Is this acceptable? Endian won't add any more transitive dependencies, and [module level](https://pdimov.github.io/boostdep-report/boost-1.79.0/module-levels.html) for JSON will stay at 8.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2022-05-25 17:34:30 UTC  
> Url: https://github.com/boostorg/json/issues/366#issuecomment-1137615278  

Yes its ok
