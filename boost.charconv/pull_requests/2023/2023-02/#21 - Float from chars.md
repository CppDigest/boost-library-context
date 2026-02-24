# #21 Float from chars [Merged]

> Username: mborland  
> Created at: 2023-02-16 19:22:18 UTC  
> Updated at: 2023-03-03 19:41:03 UTC  
> Merged at: 2023-03-03 19:41:00 UTC  
> Closed at: 2023-03-03 19:41:00 UTC  
> Url: https://github.com/boostorg/charconv/pull/21  



---

## Comment 1

> Username: mborland  
> Created_at: 2023-03-01 19:26:17 UTC  
> Url: https://github.com/boostorg/charconv/pull/21#issuecomment-1450731274  

@pdimov I think doing a separate PR for 80 and 128 bit long doubles is a good idea like we did for integers. Similar to how `float` uses the `double` implementation I am going to have 80 bit long double use the 128 bit implementation.

---
