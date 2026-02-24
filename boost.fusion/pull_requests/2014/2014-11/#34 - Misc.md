# #34 Misc... [Merged]

> Username: Flast  
> Created at: 2014-11-08 16:34:42 UTC  
> Updated at: 2014-11-08 23:26:20 UTC  
> Merged at: 2014-11-08 23:26:20 UTC  
> Closed at: 2014-11-08 23:26:20 UTC  
> Url: https://github.com/boostorg/fusion/pull/34  

### summary  
- Some headers are already renamed  
  - 4835310 Remove unnecessary header, see 5adcb5b  
- `equal_to` v.s. `boost_tuple_iterator`  
  - 5f416e2 Use category_of to get correct iterator category.  
  - 7a7b225 Add boost_tuple_iterator_identity to compare iterator correctly.  
- Because there is no documents and tests for `type_traits::ice_and`, use `mpl::and_` instead.  
  - bd5ce92 Replace type_traits::ice_and with well documented mpl::and_  
- C++11 constexpr v.s. C++14 constexpr  
  - a7e9bb2 Fix to meet constexpr requirements  
- from svn.boost.org  
  - 20ee8d1 Fix missing includes, close ticket [#8457](https://svn.boost.org/trac/boost/ticket/8457).  
  - ff56bea Remove use of `boost::blank`, close [#8622](https://svn.boost.org/trac/boost/ticket/8622)  
  
Tested under x86_64-linux-gnu with GCC 4.9.1/Clang 3.5.0/GCC 4.4.7 (both of C++11/C++98 mode).

---
