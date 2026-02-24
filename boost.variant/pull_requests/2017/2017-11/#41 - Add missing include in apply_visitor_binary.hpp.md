# #41 Add missing include in apply_visitor_binary.hpp [Merged]

> Username: awulkiew  
> Created at: 2017-11-20 18:51:22 UTC  
> Updated at: 2017-11-28 17:04:23 UTC  
> Merged at: 2017-11-28 17:04:23 UTC  
> Closed at: 2017-11-28 17:04:23 UTC  
> Url: https://github.com/boostorg/variant/pull/41  

For boost::is_lvalue_reference.

---

## Comment 1

> Username: awulkiew  
> Created_at: 2017-11-20 18:58:18 UTC  
> Updated_at: 2017-11-20 18:59:29 UTC  
> Url: https://github.com/boostorg/variant/pull/41#issuecomment-345793266  

Currently `boost/type_traits/is_lvalue_reference.hpp` is included only in `boost/variant/get.hpp` and `boost/variant/detail/multivisitors_cpp11_based.hpp`. In case when neither of these files is included the compilation fails:  
  
    In file included from ../libs/geometry/test/algorithms/convert.cpp:16:  
    In file included from ../libs/geometry/test/algorithms/test_convert.hpp:13:  
    In file included from ../boost/geometry/algorithms/assign.hpp:28:  
    In file included from ../boost/variant/apply_visitor.hpp:17:  
    ../boost/variant/detail/apply_visitor_binary.hpp:137:26: error: no member named 'is_lvalue_reference' in namespace 'boost'  
            , ! ::boost::is_lvalue_reference<Value1>::value  
                ~~~~~~~~~^  
  
from  
  
http://www.boost.org/development/tests/develop/developer/output/teeks99-02-dc3-0-11-Docker-64on64-boost-bin-v2-libs-geometry-test-algorithms-algorithms_convert-test-clang-gnu-linux-3-0~c++11-debug-threadapi-pthread.html

---

## Comment 2

> Username: awulkiew  
> Created_at: 2017-11-20 19:01:57 UTC  
> Updated_at: 2017-11-20 19:02:12 UTC  
> Url: https://github.com/boostorg/variant/pull/41#issuecomment-345794282  

This is also a problem in master and boost-1.66.0 Beta1 RC1.

---
