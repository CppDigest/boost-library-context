# #21 Small fix for those using -Wsign-conversion [Merged]

> Username: igaztanaga  
> Created at: 2021-10-11 11:13:31 UTC  
> Updated at: 2021-10-11 18:07:07 UTC  
> Merged at: 2021-10-11 18:07:06 UTC  
> Closed at: 2021-10-11 18:07:06 UTC  
> Url: https://github.com/boostorg/throw_exception/pull/21  



---

## Comment 1

> Username: pdimov  
> Created_at: 2021-10-11 11:43:13 UTC  
> Url: https://github.com/boostorg/throw_exception/pull/21#issuecomment-939952333  

Or maybe just change `I` to `std::size_t`, for those whose compilers warn on C style casts? :-)

---

## Comment 2

> Username: igaztanaga  
> Created_at: 2021-10-11 11:59:18 UTC  
> Url: https://github.com/boostorg/throw_exception/pull/21#issuecomment-939962967  

Sure, changing `I` to `std::size_t` seems cleaner ;-)

---
