# #543 Removed two instances of boost.mpl (Issue #483) [Closed]

> Username: Cypre55  
> Created at: 2021-01-24 15:21:16 UTC  
> Updated at: 2021-01-27 19:44:31 UTC  
> Closed at: 2021-01-27 16:46:30 UTC  
> Url: https://github.com/boostorg/gil/pull/543  

### Description  
  
This address Issue #483   
Removed "#include <boost/mp11/mpl.hpp>"  
at include/boost/gil/detail/mp11.hpp  
and test/core/pixel/test_fixture.hpp  
  
### Tasklist  
  
- [x] Removed instances of boost.mpl  
- [ ] Ensure all CI builds pass  
- [ ] Review and approve

---

## Review 1 [Changes requested]

> Username: mloskot  
> Created_at: 2021-01-26 19:02:02 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/543#pullrequestreview-576667512  

Could you update this PR to get the latest changes from the `develop` branch?

---

## Comment 2

> Username: Cypre55  
> Created_at: 2021-01-27 16:56:43 UTC  
> Url: https://github.com/boostorg/gil/pull/543#issuecomment-768424259  

Hi @mloskot,  
I created a new PR #551. It is up to date with develop.

---

## Comment 3

> Username: mloskot  
> Created_at: 2021-01-27 19:42:20 UTC  
> Url: https://github.com/boostorg/gil/pull/543#issuecomment-768530579  

Please next time avoid breeding new PRs for the same changes and just update one PR you created

---

## Comment 4

> Username: Cypre55  
> Created_at: 2021-01-27 19:44:31 UTC  
> Url: https://github.com/boostorg/gil/pull/543#issuecomment-768531789  

Sure  
I was getting used the whole GitHub thing. I won't happen again.

---
