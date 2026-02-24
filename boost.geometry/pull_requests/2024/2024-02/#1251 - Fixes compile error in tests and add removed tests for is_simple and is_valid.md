# #1251 Fixes compile error in tests and add removed tests for is_simple and is_valid [Merged]

> Username: vissarion  
> Created at: 2024-02-28 09:19:01 UTC  
> Updated at: 2024-04-01 11:29:11 UTC  
> Merged at: 2024-02-28 11:31:06 UTC  
> Closed at: 2024-02-28 11:31:06 UTC  
> Url: https://github.com/boostorg/geometry/pull/1251  

Following comment https://github.com/boostorg/geometry/pull/1237#discussion_r1501810625 this PR adds removed tests for is_simple and is_valid with geometries whose coordinates are close to the maximum double.   
The tests are passing with the fix of https://github.com/boostorg/geometry/pull/1237 but the results are different i.e. the intersection does not create NaN coordinates, it is more accurate but still can create invalid geometries.  
  
This PR also fixes a compile error in tests (wrong namespace for numeric_cast).

---

## Review 1 [Commented]

> Username: barendgehrels  
> Created_at: 2024-02-28 11:12:17 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1251#pullrequestreview-1905977008  

📁 test/util/rational.cpp

```diff
  37 |-     BOOST_CHECK_EQUAL(bg::detail::numeric_cast<int>(r2), 2);
  36 |+     BOOST_CHECK_CLOSE(bg::util::numeric_cast<double>(r2), 2.5, 0.00001);
  37 |+     BOOST_CHECK_EQUAL(bg::util::numeric_cast<int>(r2), 2);
```

> Username: barendgehrels  
> Created_at: 2024-02-28 11:12:17 UTC  
> Updated_at: 2024-02-28 11:12:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/1251#discussion_r1505779975  

Oops. As soon as the Cmake PR is merged ,this won't happen anymore ;-)  
Thanks for fixing.

> Username: barendgehrels  
> Created_at: 2024-02-28 11:20:15 UTC  
> Url: https://github.com/boostorg/geometry/pull/1251#discussion_r1505789218  

I see it was just approved, thanks, it's merged now


---

## Review 2 [Approved]

> Username: barendgehrels  
> Created_at: 2024-02-28 11:12:42 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1251#pullrequestreview-1905977735  

looks good to me

---
