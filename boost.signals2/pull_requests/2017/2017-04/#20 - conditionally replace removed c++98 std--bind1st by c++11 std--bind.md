# #20 conditionally replace removed c++98 std::bind1st by c++11 std::bind [Closed]

> Username: DanielaE  
> Created at: 2017-04-19 17:59:32 UTC  
> Updated at: 2017-04-22 08:03:30 UTC  
> Closed at: 2017-04-21 17:08:12 UTC  
> Url: https://github.com/boostorg/signals2/pull/20  

Signed-off-by: Daniela Engert <dani@ngrt.de>

---

## Comment 1

> Username: fmhess  
> Created_at: 2017-04-21 16:55:05 UTC  
> Url: https://github.com/boostorg/signals2/pull/20#issuecomment-296245401  

If BOOST_NO_CXX98_BINDERS is defined, the tests will fail.  Maybe it would be easier just to use boost.bind?

---

## Comment 2

> Username: fmhess  
> Created_at: 2017-04-21 17:08:12 UTC  
> Url: https://github.com/boostorg/signals2/pull/20#issuecomment-296248692  

I've changed signal_test and signal_n_test to use boost.bind.

---

## Comment 3

> Username: DanielaE  
> Created_at: 2017-04-22 08:03:30 UTC  
> Url: https://github.com/boostorg/signals2/pull/20#issuecomment-296356355  

Frank, I can't see how the tests would fail with BOOST_NO_CXX98_BINDERS defined. I've tested that with VC14.0 and VC14.1 in C++17 mode - the only compilers and the only configuration where it is defined at all. Everything else (i.e. the whole test matrix) is run **without** BOOST_NO_CXX98_BINDERS defined.

---
