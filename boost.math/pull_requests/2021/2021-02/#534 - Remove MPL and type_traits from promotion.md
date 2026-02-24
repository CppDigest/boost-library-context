# #534 Remove MPL and type_traits from promotion [Merged]

> Username: mborland  
> Created at: 2021-02-13 11:30:44 UTC  
> Updated at: 2021-02-14 11:25:55 UTC  
> Merged at: 2021-02-13 19:22:44 UTC  
> Closed at: 2021-02-13 19:22:45 UTC  
> Url: https://github.com/boostorg/math/pull/534  



---

## Comment 1

> Username: mborland  
> Created_at: 2021-02-13 16:46:57 UTC  
> Url: https://github.com/boostorg/math/pull/534#issuecomment-778643754  

@jzmaddock All of these MPL based PRs should be good to go. Once they are all in I can make the final push to remove MPL use from the policies.

---

## Review 2 [Commented]

> Username: jzmaddock  
> Created_at: 2021-02-13 19:22:35 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/534#pullrequestreview-589989610  

📁 include/boost/math/tools/promotion.hpp

```diff
 137 |          //
 148 |-          BOOST_STATIC_ASSERT_MSG((0 == ::boost::is_same<type, long double>::value), "Sorry, but this platform does not have sufficient long double support for the special functions to be reliably implemented.");
 138 |+          BOOST_STATIC_ASSERT_MSG((0 == std::is_same<type, long double>::value), "Sorry, but this platform does not have sufficient long double support for the special functions to be reliably implemented.");
```

> Username: jzmaddock  
> Created_at: 2021-02-13 19:22:35 UTC  
> Url: https://github.com/boostorg/math/pull/534#discussion_r575703387  

These BOOST_STATIC_ASSERT's could be real `static_assert`'s now, but we can do that later ;)


---
