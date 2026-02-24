# #48 uncommented an assert [Closed]

> Username: akrzemi1  
> Created at: 2017-03-17 23:13:36 UTC  
> Updated at: 2018-09-23 22:26:36 UTC  
> Closed at: 2017-04-08 21:50:19 UTC  
> Url: https://github.com/boostorg/safe_numerics/pull/48  

Ever since C++14, you can use assertions in constexpr functions. Even if you want to support older constexpr, you can use a C++11-cmpatible trick, as used [here](https://github.com/akrzemi1/Optional/blob/master/optional.hpp#L199-L203).

---

## Comment 1

> Username: robertramey  
> Created_at: 2017-04-08 21:50:00 UTC  
> Url: https://github.com/boostorg/safe_numerics/pull/48#issuecomment-292748096  

you're correct, fixed

---
