# #169 - self sufficient header [Closed]

> Username: ohhmm  
> Created at: 2019-11-20 09:59:12 UTC  
> Updated at: 2019-12-05 16:27:24 UTC  
> Closed at: 2019-12-05 16:27:24 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/169  

The original issue is there https://github.com/boostorg/boost/issues/301#issuecomment-555628971  
  
self sufficient header is a header which requires no other headers to be included before the header.  
the example of an introduced issue because of introduced header non-self-sufficiency is multiprecision/roots.hpp in 1.70  
an automated test of boost headers self-sufficiency would scan all boost headers and compile its inclusion lines. if compilation successful then the header test passed well.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2019-11-24 12:13:43 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/169#issuecomment-557882849  

There is no header roots.hpp in Multiprecision.  Which file were you referring to specifically?

---

## Comment 2

> Username: jzmaddock  
> Created at: 2019-11-25 17:51:16 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/169#issuecomment-558267192  

I've just done a quick search, and the only roots.hpp anywhere in boost is boost/math/tools/roots.hpp and that has a self-sufficiency / include test already in https://github.com/boostorg/math/blob/develop/test/compile_test/tools_roots_inc_test.cpp.  
  
Closing for now, please reopen if you have further information.

---

## Comment 3

> Username: ohhmm  
> Created at: 2019-12-04 20:17:01 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/169#issuecomment-561820942  

Its using boost::multiprecision::number_kind_complex which is not in includes

---

## Comment 4

> Username: jzmaddock  
> Created at: 2019-12-05 16:27:23 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/169#issuecomment-562205016  

That was fixed in https://github.com/boostorg/math/commit/d77474460a80991f968dbfef48ce4af7e1693fdf#diff-cacc472e10fe43e99ce7ed867fe88fff - there are no longer any references to `boost::multiprecision::number_kind_complex` anywhere in Boost.Math.
