# #543 Enable c++11,14 use on compilers with <execution> [Closed]

> Username: mborland  
> Created at: 2021-02-20 08:20:43 UTC  
> Updated at: 2021-02-20 13:39:13 UTC  
> Closed at: 2021-02-20 11:40:35 UTC  
> Url: https://github.com/boostorg/math/pull/543  

BOOST_NO_CXX17_HDR_EXECUTION fails as a guard if using std=c++11 or c++14 on a compiler that has `<execution>` e.g. GCC10 (default is c++14). Found while poking around on issue #535. @jzmaddock I know we have been going back and forth on this logic, but I think this addition gets it right.

---

## Review 1 [Commented]

> Username: jzmaddock  
> Created_at: 2021-02-20 10:24:26 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/543#pullrequestreview-594738584  

📁 include/boost/math/statistics/bivariate_statistics.hpp

```diff
  22 | // https://en.cppreference.com/w/cpp/compiler_support
  24 |- #ifndef BOOST_NO_CXX17_HDR_EXECUTION
  23 |+ #if !defined(BOOST_NO_CXX17_HDR_EXECUTION) && (__cplusplus > 201700L || _MSVC_LANG > 201700L)
```

> Username: jzmaddock  
> Created_at: 2021-02-20 10:24:25 UTC  
> Updated_at: 2021-02-20 10:24:26 UTC  
> Url: https://github.com/boostorg/math/pull/543#discussion_r579636640  

This is Boost.Config's responsibility to get right, plus this should already be handled here: https://github.com/boostorg/config/blob/1c45d765f107e0fcfc45a075c55bd66780e98188/include/boost/config/detail/suffix.hpp#L1096   
  
What am I missing?

> Username: mborland  
> Created_at: 2021-02-20 11:36:11 UTC  
> Url: https://github.com/boostorg/math/pull/543#discussion_r579644709  

That looks correct. Is that new to the develop branch? I link against release 1.75 and it only has optional, string_view, and variant.

> Username: jzmaddock  
> Created_at: 2021-02-20 13:39:13 UTC  
> Url: https://github.com/boostorg/math/pull/543#discussion_r579658620  

Yes develop.


---
