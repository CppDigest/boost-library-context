# #480 Add integer support to bivariate statistics [Merged]

> Username: mborland  
> Created at: 2021-01-04 19:55:33 UTC  
> Updated at: 2021-01-07 16:22:16 UTC  
> Merged at: 2021-01-05 18:57:17 UTC  
> Closed at: 2021-01-05 18:57:17 UTC  
> Url: https://github.com/boostorg/math/pull/480  

Add support for integers to bivariate statistics. Add unit tests with same tolerances as Real types. Remove uses of structured bindings and `std::size()` to reduce the required language standard from 17 to 11.

---

## Comment 1

> Username: mborland  
> Created_at: 2021-01-04 20:01:28 UTC  
> Url: https://github.com/boostorg/math/pull/480#issuecomment-754186159  

@NAThompson I realize the irony that I am turning around and taking the opposite stance on the `u.size()` vs `std::size(u)` argument. I forgot that it was a c++17 language feature.

---

## Review 2 [Commented]

> Username: NAThompson  
> Created_at: 2021-01-04 21:25:19 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/480#pullrequestreview-561355984  

📁 include/boost/math/statistics/bivariate_statistics.hpp

```diff
  16 |- template<class Container>
  17 |- auto means_and_covariance(Container const & u, Container const & v)
  20 |+ template<class ReturnType, class Container>
```

> Username: NAThompson  
> Created_at: 2021-01-04 21:25:19 UTC  
> Updated_at: 2021-01-05 15:52:41 UTC  
> Url: https://github.com/boostorg/math/pull/480#discussion_r551577514  

This will be a breaking change. Ideally I'd like the return type deduced. There are a few examples where I used an `if constexpr` to determine if the input type was integral, and then returned `double`.

> Username: mborland  
> Created_at: 2021-01-05 15:39:38 UTC  
> Updated_at: 2021-01-05 15:52:41 UTC  
> Url: https://github.com/boostorg/math/pull/480#discussion_r552011249  

It is a breaking change so it got renamed and moved into the detail namespace. The user facing functions remain the same and offer return type deduction via `std::enable_if` (lines 93-103). I can move implementation details into a separate file to make this more clear. I did not do that already because this file is still pretty small.


---

## Comment 3

> Username: mborland  
> Created_at: 2021-01-05 15:52:00 UTC  
> Url: https://github.com/boostorg/math/pull/480#issuecomment-754721689  

CI is clean minus 1 test where it tried to compile in c++03 mode and failed with error `error C4519: default template arguments are only allowed on a class template`. I added requires `cxx11_sfinae_expr` to the Jamfile to resolve.

---

## Comment 4

> Username: NAThompson  
> Created_at: 2021-01-05 16:19:35 UTC  
> Url: https://github.com/boostorg/math/pull/480#issuecomment-754739377  

Ok, the structured bindings still work on the *user* end right? You just removed them so the tests would pass in C++11 mode?  
  
If so, I'm happy with these changes.  
  
@jzmaddock : Anything I missed?

---

## Comment 5

> Username: mborland  
> Created_at: 2021-01-05 16:44:19 UTC  
> Url: https://github.com/boostorg/math/pull/480#issuecomment-754753687  

> Ok, the structured bindings still work on the _user_ end right? You just removed them so the tests would pass in C++11 mode?  
  
That is correct; structured bindings still work and the user is free to use them.

---

## Comment 6

> Username: jzmaddock  
> Created_at: 2021-01-05 18:37:50 UTC  
> Url: https://github.com/boostorg/math/pull/480#issuecomment-754821873  

@NAThompson : The interface changes look good to me, I haven't looked in detail at the implementation details.

---
