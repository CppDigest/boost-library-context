# #120 [clang] Fix [-Wc++11-narrowing] error [Closed]

> Username: hia3  
> Created at: 2017-09-19 15:47:25 UTC  
> Updated at: 2018-01-29 09:49:05 UTC  
> Closed at: 2018-01-29 09:49:05 UTC  
> Url: https://github.com/boostorg/test/pull/120  

Compiling on Windows with clang this code:  
  
#define BOOST_TEST_MODULE The Test  
#include <boost/test/included/unit_test.hpp>  
  
command:  
  
"clang.exe" -c -x c++ test.cpp -Iboost -fno-ms-compatibility  
  
produces:  
  
boost/test/utils/basic_cstring/basic_cstring.hpp:64:29: error: enumerator value evaluates to 18446744073709551615, which cannot be narrowed to type 'int' [-Wc++11-narrowing]  
  
Not tested it with IBM/VisualAge, but boost\config\compiler\vacpp.hpp only defines BOOST_NO_INCLASS_MEMBER_INITIALIZATION for IBMCPP <= 502, so BOOST_STATIC_CONSTANT should expand to "static const". Anyway, if anything go wrong, it is BOOST_STATIC_CONSTANT to blame, not Boost.Test.

---

## Comment 1

> Username: raffienficiaud  
> Created_at: 2018-01-29 06:20:04 UTC  
> Url: https://github.com/boostorg/test/pull/120#issuecomment-361150521  

I merged your patch and rebased onto develop: branch ``topic/PR120-fix-narrowing-errors``. It would be good if you can test on your end.

---

## Comment 2

> Username: hia3  
> Created_at: 2018-01-29 09:32:41 UTC  
> Url: https://github.com/boostorg/test/pull/120#issuecomment-361188217  

Works. Thanks.

---
