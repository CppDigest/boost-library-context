# #169 - Optimize `axis(i)` for special case of `std::tuple<A, A, A>` axis [Closed]

> Username: HDembinski  
> Created at: 2019-03-07 10:42:07 UTC  
> Updated at: 2019-03-15 08:03:58 UTC  
> Closed at: 2019-03-15 08:03:58 UTC  
> Url: https://github.com/boostorg/histogram/issues/169  

Optimize `axis(i)` for the case when you have a `std::tuple<A, A, A>` where all types are the same to return a const A& instead of axis::variant<const A&> in this case.

---

## Comment 1

> Username: HDembinski  
> Created at: 2019-03-07 10:44:18 UTC  
> Url: https://github.com/boostorg/histogram/issues/169#issuecomment-470477283  

Check if this case is actually handled properly in a test.

---

## Comment 2

> Username: HDembinski  
> Created at: 2019-03-15 08:03:58 UTC  
> Url: https://github.com/boostorg/histogram/issues/169#issuecomment-473193737  

fixed in develop
