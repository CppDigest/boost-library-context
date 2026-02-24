# #122 Specialize range_const_iterator_helper for std::span [Open]

> Username: ecatmur  
> Created at: 2021-02-04 09:43:50 UTC  
> Updated at: 2022-02-08 05:24:31 UTC  
> Url: https://github.com/boostorg/range/pull/122  

std::span [does not have const_iterator member type](https://cplusplus.github.io/LWG/lwg-defects.html#3320), so this fails:  
  
    #include <span>  
    #include <boost/range/iterator.hpp>  
    boost::range_iterator<std::span<int> const>::type i;  
    //                                           ^~~~  
    // error: 'type' in 'struct boost::range_iterator<const std::span<int> >' does not name a type  
  
also `boost::begin()` and `boost::end()` fail, and anything that calls them (e.g. `boost::size()`).  
  
[Demo](https://godbolt.org/z/3sb6xr).  
  
I'm using 201902 as the feature test value on the basis that an implementation may have removed `const_iterator` (fixing LWG 3320) but not yet applied P1976R2 which updates the feature test to 202002L.

---

## Comment 1

> Username: Kojoley  
> Created_at: 2021-02-09 23:10:04 UTC  
> Url: https://github.com/boostorg/range/pull/122#issuecomment-776306088  

FYI: it was caused by http://wg21.link/lwg3320 resolution and a subject of [P2276](https://github.com/cplusplus/papers/issues/969).

---

## Comment 2

> Username: timblechmann  
> Created_at: 2021-12-29 14:36:22 UTC  
> Url: https://github.com/boostorg/range/pull/122#issuecomment-1002624994  

any update on this?

---

## Comment 3

> Username: glenfe  
> Created_at: 2021-12-30 01:57:36 UTC  
> Url: https://github.com/boostorg/range/pull/122#issuecomment-1002840248  

I think we need to fix up the CI for testing this i.e. Appveyor and Github Actions (Travis is dead to us now). On top of that, this PR will need unit tests.

---

## Comment 4

> Username: ecatmur  
> Created_at: 2021-12-30 16:57:57 UTC  
> Url: https://github.com/boostorg/range/pull/122#issuecomment-1003108300  

> FYI: it was caused by http://wg21.link/lwg3320 resolution and a subject of [P2276](https://github.com/cplusplus/papers/issues/969).  
  
Thanks, yeah this is tricky - and also `const_iterator` could be added back in future, it looks like.   
  
I've taken the approach that we want the simplest possible solution that could work, which for now is ignoring `const` on top of `std::span`, and added enough tests that we'll find out if the Standard breaks us in future.

---
