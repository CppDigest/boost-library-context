# #28 Fixes for cxx11 changes to dependencies: [Merged]

> Username: jzmaddock  
> Created at: 2021-02-18 18:29:59 UTC  
> Updated at: 2021-02-18 19:47:30 UTC  
> Merged at: 2021-02-18 19:46:31 UTC  
> Closed at: 2021-02-18 19:46:31 UTC  
> Url: https://github.com/boostorg/integer/pull/28  

Make Multiprecision tests depend on <type_traits> and BOOST_NO_CXX11_HDR_TYPE_TRAITS.  
Use specific Boost.Random headers and not <boost/random.hpp> as the latter pulls in some of Boost.Math which has now moved to C++11.

---

## Comment 1

> Username: Lastique  
> Created_at: 2021-02-18 19:47:30 UTC  
> Url: https://github.com/boostorg/integer/pull/28#issuecomment-781593133  

Thanks!

---
