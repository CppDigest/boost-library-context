# #2 Allow use of make_recursive_variant with std::tuple [Closed]

> Username: ecatmur  
> Created at: 2014-04-09 21:47:46 UTC  
> Updated at: 2014-07-10 08:36:17 UTC  
> Closed at: 2014-04-11 00:27:28 UTC  
> Url: https://github.com/boostorg/variant/pull/2  

Per http://stackoverflow.com/questions/22963967/using-boostmake-recursive-variant-with-tuple make_recursive_variant breaks with variadic class templates e.g. std::tuple.  Add a specialization of boost::detail::variant::substitute to handle variadic class templates, and test.

---

## Comment 1

> Username: apolukhin  
> Created_at: 2014-04-10 07:58:28 UTC  
> Url: https://github.com/boostorg/variant/pull/2#issuecomment-40052255  

This is a good patch.  
  
Could you please make a pull request against the `develop` branch (currently it is `master`).  
Please also add to the `test/recursive_variant_test.cpp` following checks: `&& !defined(BOOST_NO_CXX11_HDR_TUPLE)`. There can be a compiler, that has variadic templates and has no `std::tuple`.

---
