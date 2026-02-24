# #55 Fixed visitor result type deduction at rvalue ref operators [Merged]

> Username: Kojoley  
> Created at: 2019-01-03 19:58:45 UTC  
> Updated at: 2019-01-05 17:24:23 UTC  
> Merged at: 2019-01-05 17:15:57 UTC  
> Closed at: 2019-01-05 17:15:57 UTC  
> Url: https://github.com/boostorg/variant/pull/55  



---

## Comment 1

> Username: coveralls  
> Created_at: 2019-01-03 20:25:28 UTC  
> Updated_at: 2019-01-05 17:24:22 UTC  
> Url: https://github.com/boostorg/variant/pull/55#issuecomment-451266426  

[![Coverage Status](https://coveralls.io/builds/20920794/badge)](https://coveralls.io/builds/20920794)  
  
Coverage remained the same at 95.063% when pulling **9af72f37bcd2de5234b87ce7c0e0ad7a57f41c43 on Kojoley:fix-apply_visitor-decltype-result_type-deduction** into **4addd1022ae01d7abd62e13bcf65b355f1db51ec on boostorg:develop**.

---

## Comment 2

> Username: Kojoley  
> Created_at: 2019-01-03 21:45:33 UTC  
> Url: https://github.com/boostorg/variant/pull/55#issuecomment-451287486  

It looks like I found another bug, but I do not have GCC 4.6 to investigate it. Should I blacklist it from running the test?

---

## Comment 3

> Username: apolukhin  
> Created_at: 2019-01-04 23:33:37 UTC  
> Url: https://github.com/boostorg/variant/pull/55#issuecomment-451600527  

Just blacklist it along with GCC 4.7  
  
GCC < 4.8 have wired C++11 support for rvalues. IMO it should be disabled in Boost.Config

---

## Comment 4

> Username: Kojoley  
> Created_at: 2019-01-05 17:03:51 UTC  
> Url: https://github.com/boostorg/variant/pull/55#issuecomment-451672171  

I've changed macro to `BOOST_NO_CXX11_REF_QUALIFIERS` and it solved the issue as `boost::apply_visitor` calls variant type `apply_visitor` method which relies on ref qualifiers to detect moving.

---

## Comment 5

> Username: apolukhin  
> Created_at: 2019-01-05 17:16:14 UTC  
> Url: https://github.com/boostorg/variant/pull/55#issuecomment-451673131  

Many thanks for the PR!

---
