# #48 Remove explicit check for <forward_list> [Closed]

> Username: pdimov  
> Created at: 2016-11-10 12:46:30 UTC  
> Updated at: 2017-05-23 22:10:29 UTC  
> Closed at: 2017-05-23 22:10:29 UTC  
> Url: https://github.com/boostorg/serialization/pull/48  

This removes the `#error` directives when `BOOST_NO_CXX11_HDR_FORWARD_LIST` is defined, to make the tests pass on clang-darwin.

---
