# #23 Improve constexpr supports [Merged]

> Username: Flast  
> Created at: 2014-10-20 15:10:00 UTC  
> Updated at: 2014-10-21 15:00:50 UTC  
> Merged at: 2014-10-21 14:55:54 UTC  
> Closed at: 2014-10-21 14:55:54 UTC  
> Url: https://github.com/boostorg/fusion/pull/23  

- fusion::fold (and other variations) now satisfy C++11 constexpr requirements.  
  - will fix [Test output: BP x86_64 C++11 - fusion - deque_hash / gcc-4.8.3~c11](http://www.boost.org/development/tests/develop/developer/output/BP%20x86_64%20C++11-boost-bin-v2-libs-fusion-test-deque_hash-test-gcc-4-8-3~c11-debug-debug-symbols-off.html) and many other failures ...  
- fusion::swap now uses BOOST_CXX14_CONSTEXPR instead of BOOST_CONSTEXPR.

---

## Comment 1

> Username: djowel  
> Created_at: 2014-10-21 14:55:48 UTC  
> Url: https://github.com/boostorg/fusion/pull/23#issuecomment-59940568  

This looks really good! Thank you very much.

---
