# #248 Fix removed std::result_of for async with MSVC and C++20 [Merged]

> Username: ctrysbita  
> Created at: 2020-05-22 14:55:16 UTC  
> Updated at: 2020-06-26 05:31:32 UTC  
> Merged at: 2020-06-26 05:31:26 UTC  
> Closed at: 2020-06-26 05:31:26 UTC  
> Url: https://github.com/boostorg/fiber/pull/248  

- std::result_of has been deprecated in C++17 and will be removed in C++20.  
- It has been removed by latest MSVC standard library with C++20 enabled. The package can't be compiled when flag `/std:c++latest` is set.  
  
#### Reference  
https://github.com/boostorg/asio/commit/69a6480fc0640288226f71b147a2c7847277a431

---

## Comment 1

> Username: ctrysbita  
> Created_at: 2020-05-23 03:08:46 UTC  
> Url: https://github.com/boostorg/fiber/pull/248#issuecomment-632976406  

Test failure is because of clang implementation, which is not associated with the code modified.

---

## Comment 2

> Username: olk  
> Created_at: 2020-06-26 05:31:32 UTC  
> Url: https://github.com/boostorg/fiber/pull/248#issuecomment-649978396  

ty

---
