# #37 Use Boost's unary function instead of STL's as it is deprecated in C++17 [Closed]

> Username: pavelkryukov  
> Created at: 2017-11-20 12:52:34 UTC  
> Updated at: 2017-11-20 15:29:28 UTC  
> Closed at: 2017-11-20 13:30:31 UTC  
> Url: https://github.com/boostorg/iterator/pull/37  



---

## Comment 1

> Username: Lastique  
> Created_at: 2017-11-20 13:30:31 UTC  
> Url: https://github.com/boostorg/iterator/pull/37#issuecomment-345695692  

Using implementation details in examples is not right. I've removed the use of `std::unary_function` in 30b93d742810d1a0bfb25aa81d8b011fcbecb6de.

---

## Comment 2

> Username: pavelkryukov  
> Created_at: 2017-11-20 15:29:28 UTC  
> Url: https://github.com/boostorg/iterator/pull/37#issuecomment-345730092  

Thank you!

---
