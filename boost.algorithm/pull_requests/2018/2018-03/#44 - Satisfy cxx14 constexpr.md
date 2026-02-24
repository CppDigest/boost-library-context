# #44 Satisfy cxx14 constexpr [Closed]

> Username: Flast  
> Created at: 2018-03-17 07:50:12 UTC  
> Updated at: 2018-04-05 06:38:59 UTC  
> Closed at: 2018-04-04 18:19:25 UTC  
> Url: https://github.com/boostorg/algorithm/pull/44  

This PR fixes compile errors regarding c++14 constexpr:  
- `one_of(_equal)` and `equal` are marked as c++14 constexpr but standard algorithms are not constexpr until c++20  
- `clamp_test` tests c++14 constexpr but some objects are not constexpr  
  
Tested compilers:  
- gcc 8.0.1 20180218 (Red Hat 8.0.1-0.14) w/ cxxstd=03,11,14,17  
- 6.0.0 (tags/RELEASE_600/rc2) w/ cxxstd=03,11,14,17

---

## Comment 1

> Username: mclow  
> Created_at: 2018-04-04 18:19:25 UTC  
> Url: https://github.com/boostorg/algorithm/pull/44#issuecomment-378696735  

I have applied this manually [ committed in 3af7aca ], with a couple other test fixes. Thanks!

---
