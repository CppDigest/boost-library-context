# #64 Fix clang compilation when T is constructible from other types [Merged]

> Username: Lastique  
> Created at: 2018-11-04 15:00:16 UTC  
> Updated at: 2018-11-05 19:10:50 UTC  
> Merged at: 2018-11-05 17:17:44 UTC  
> Closed at: 2018-11-05 17:17:44 UTC  
> Url: https://github.com/boostorg/optional/pull/64  

The problem was with copy/move constructors of `boost::optional<T>`, which invoked `optional_base<T>` constructors with a single argument. Since `optional_base` has copy/move constructors along with initializing constructors taking a single argument, the latter may be considered by the compiler for viability. While doing so, the compiler may instantiate the template constructor of `T` with an argument of `optional_base<T>`, which in turn may fail if the constructor attempts to inspect the type of its argument (e.g. to constrain the set of acceptable types). Specifically, this happens with clang in C++03 mode, when `boost::multiprecision::number` is wrapped in `boost::optional` and a copy constructor of `boost::optional` is invoked.  
  
This PR fixes the problem by destinguishing copy/move constructors of `optional_base` from initializing constructors with an additional tag argument. The PR also adds a test.

---

## Comment 1

> Username: Lastique  
> Created_at: 2018-11-04 15:10:25 UTC  
> Url: https://github.com/boostorg/optional/pull/64#issuecomment-435677491  

Reference to the problem with Boost.Multiprecision: https://github.com/boostorg/integer/pull/11#issuecomment-435649481.

---
