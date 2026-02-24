# #104 Fix -Wmissing-noreturn clang warnings [Merged]

> Username: MarcelRaad  
> Created at: 2019-02-25 09:08:39 UTC  
> Updated at: 2019-02-26 23:15:47 UTC  
> Merged at: 2019-02-26 23:15:47 UTC  
> Closed at: 2019-02-26 23:15:47 UTC  
> Url: https://github.com/boostorg/container/pull/104  

Mark functions only throwing exceptions or calling `std::abort` with  
`BOOST_NORETURN`.

---
