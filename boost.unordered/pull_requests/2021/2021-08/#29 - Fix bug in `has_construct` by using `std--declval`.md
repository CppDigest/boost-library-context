# #29 Fix bug in `has_construct` by using `std::declval` [Merged]

> Username: cmazakas  
> Created at: 2021-08-28 23:12:46 UTC  
> Updated at: 2021-08-31 20:59:17 UTC  
> Merged at: 2021-08-31 20:31:35 UTC  
> Closed at: 2021-08-31 20:31:35 UTC  
> Url: https://github.com/boostorg/unordered/pull/29  

`boost::unordered::detail::make<Args const>()` has the fatal flaw that this creates a `Args const&` which can invoke implicitly deleted copy constructors such as when `Args` is `std::tuple<int&&>` as is the case of some of the `rvalue(k)`-style tests.

---
