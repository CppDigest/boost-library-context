# #87 Fix comments in uniform_real_distribution [Merged]

> Username: paulharris  
> Created at: 2022-05-22 00:22:38 UTC  
> Updated at: 2023-05-18 07:02:39 UTC  
> Merged at: 2023-05-13 17:21:50 UTC  
> Closed at: 2023-05-13 17:21:50 UTC  
> Url: https://github.com/boostorg/random/pull/87  

The comments state: `require min <= max`  
but the code states `BOOST_ASSERT(_min < _max);`

---

## Comment 1

> Username: paulharris  
> Created_at: 2022-05-22 00:23:55 UTC  
> Url: https://github.com/boostorg/random/pull/87#issuecomment-1133789579  

This has one extra tiny change, compared with #4

---
