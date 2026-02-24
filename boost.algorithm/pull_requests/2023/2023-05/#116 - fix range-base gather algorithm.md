# #116 fix range-base gather algorithm [Merged]

> Username: jiayuehua  
> Created at: 2023-05-08 03:08:12 UTC  
> Updated at: 2023-06-14 16:23:41 UTC  
> Merged at: 2023-06-14 16:23:41 UTC  
> Closed at: 2023-06-14 16:23:41 UTC  
> Url: https://github.com/boostorg/algorithm/pull/116  

gather algorithm is a mutation algorithm, so its range parameter should not be const reference type.

---

## Comment 1

> Username: jiayuehua  
> Created_at: 2023-06-12 05:27:57 UTC  
> Url: https://github.com/boostorg/algorithm/pull/116#issuecomment-1586606862  

@mclow

---

## Comment 2

> Username: nigels-com  
> Created_at: 2023-06-12 07:20:09 UTC  
> Url: https://github.com/boostorg/algorithm/pull/116#issuecomment-1586733456  

Looks reasonable.  
Consider cloning gather_test1.cpp for the range variant.

---
