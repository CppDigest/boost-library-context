# #8 Add integer framework [Merged]

> Username: mborland  
> Created at: 2023-01-10 17:49:26 UTC  
> Updated at: 2023-01-16 17:35:00 UTC  
> Merged at: 2023-01-16 17:34:56 UTC  
> Closed at: 2023-01-16 17:34:56 UTC  
> Url: https://github.com/boostorg/charconv/pull/8  

Adds the integer parser to `from_chars`

---

## Review 1 [Commented]

> Username: pdimov  
> Created_at: 2023-01-10 18:06:15 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/charconv/pull/8#pullrequestreview-1242737653  

📁 include/boost/charconv/from_chars.hpp

```diff
  21 |+     // 0 = no error
  22 |+     // 1 = invalid_argument
  23 |+     // 2 = result_out_of_range
```

> Username: pdimov  
> Created_at: 2023-01-10 18:06:14 UTC  
> Updated_at: 2023-01-10 18:06:29 UTC  
> Url: https://github.com/boostorg/charconv/pull/8#discussion_r1066125992  

Please use the standard values of EINVAL and ERANGE from `<cerrno>`, to match the errc values.


---

## Comment 2

> Username: mborland  
> Created_at: 2023-01-16 16:17:54 UTC  
> Url: https://github.com/boostorg/charconv/pull/8#issuecomment-1384276922  

@pdimov Any other comments before I merge this? I am happy with where it sits, and added a test set to ensure that the results matched the STL implementation.

---

## Comment 3

> Username: pdimov  
> Created_at: 2023-01-16 16:29:15 UTC  
> Url: https://github.com/boostorg/charconv/pull/8#issuecomment-1384293985  

Looks good.  
  
BOOST_CHARCONV_ASSERT should use BOOST_ASSERT instead of plain assert.  
  
It's possible to use `BOOST_TEST( x == y )` instead of `BOOST_TEST_EQ( x, y )` in the case where `x` isn't streamable; there's no need to drop to assert.  
  
constexpr does mean we need to put the integral implementation into the header after all, but it's probably worth it.

---

## Comment 4

> Username: mborland  
> Created_at: 2023-01-16 16:52:16 UTC  
> Url: https://github.com/boostorg/charconv/pull/8#issuecomment-1384318421  

Do you know the solution for [these](https://github.com/CPPAlliance/charconv/actions/runs/3932369587/jobs/6724844470#step:6:13) cmake errors? It looks like Boost.Assert is being installed but not found?

---

## Comment 5

> Username: pdimov  
> Created_at: 2023-01-16 17:03:50 UTC  
> Url: https://github.com/boostorg/charconv/pull/8#issuecomment-1384332754  

You probably need to regenerate the CMakeLists.txt file to pick up the dependency on Assert. Use `boostdep --cmake charconv` (assuming that the library is in libs/charconv in the Boost tree.)

---
