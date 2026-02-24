# #348 - Support of c++17 and c++20 [Open]

> Username: yuxianch  
> Created at: 2021-01-21 07:01:18 UTC  
> Updated at: 2021-04-17 21:15:01 UTC  
> Url: https://github.com/boostorg/python/issues/348  

Fail to compile test/back_reference.cpp with C++20 since std::auto_ptr is removed in c++17.  
https://github.com/boostorg/python/blob/cbd2d9f033c61d29d0a1df14951f4ec91e7d05cd/test/back_reference.cpp#L102

---

## Comment 1

> Username: stefanseefeld  
> Created at: 2021-04-17 21:15:01 UTC  
> Url: https://github.com/boostorg/python/issues/348#issuecomment-821888680  

It should be easy to adapt this test to use e.g. `std::unique_ptr`. The tricky part is to only conditionally do this substitution, to support pre-C++11 compilers as well as C++17 and beyond.
