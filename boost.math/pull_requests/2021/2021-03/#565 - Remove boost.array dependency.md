# #565 Remove boost.array dependency [Merged]

> Username: mborland  
> Created at: 2021-03-10 17:02:12 UTC  
> Updated at: 2021-03-21 13:13:52 UTC  
> Merged at: 2021-03-20 16:59:13 UTC  
> Closed at: 2021-03-20 16:59:13 UTC  
> Url: https://github.com/boostorg/math/pull/565  



---

## Review 1 [Commented]

> Username: jzmaddock  
> Created_at: 2021-03-10 19:13:05 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/565#pullrequestreview-609074483  

📁 include/boost/math/tools/rational.hpp

```diff
 189 | inline U evaluate_polynomial(const T* poly, U const& z, std::size_t count) BOOST_MATH_NOEXCEPT(U)
 190 | {
 190 |-    BOOST_ASSERT(count > 0);
```

> Username: jzmaddock  
> Created_at: 2021-03-10 19:13:05 UTC  
> Updated_at: 2021-03-19 14:06:55 UTC  
> Url: https://github.com/boostorg/math/pull/565#discussion_r591798184  

I'd prefer to keep BOOST_ASSERT's for now as it has some (small) advantages over assert.  
  
The long term plan would be to search and replace BOOST_ASSERT->BOOST_MATH_ASSERT and then set BOOST_MATH_ASSERT to BOOST_ASSERT when Boost is present, and to assert when it's not (ie "standalone" mode).


---

## Comment 2

> Username: mborland  
> Created_at: 2021-03-11 17:07:04 UTC  
> Url: https://github.com/boostorg/math/pull/565#issuecomment-796893795  

@jzmaddock This should be good go go. I reverted the replacement of `BOOST_ASSERT` and CI is clean.

---

## Comment 3

> Username: mborland  
> Created_at: 2021-03-19 14:08:30 UTC  
> Url: https://github.com/boostorg/math/pull/565#issuecomment-802860136  

Resolved the merge conflicts; good to go now.

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2021-03-20 16:59:08 UTC  
> Url: https://github.com/boostorg/math/pull/565#issuecomment-803409792  

Thanks Matt!

---
