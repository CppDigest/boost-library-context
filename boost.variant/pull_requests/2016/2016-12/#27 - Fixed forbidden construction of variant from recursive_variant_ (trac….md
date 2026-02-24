# #27 Fixed forbidden construction of variant from recursive_variant_ (trac… [Merged]

> Username: very-cool-name  
> Created at: 2016-12-20 10:59:26 UTC  
> Updated at: 2016-12-21 19:56:50 UTC  
> Merged at: 2016-12-21 19:43:31 UTC  
> Closed at: 2016-12-21 19:43:31 UTC  
> Url: https://github.com/boostorg/variant/pull/27  

… #12508, #12645)

---

## Review 1 [Commented]

> Username: apolukhin  
> Created_at: 2016-12-20 19:19:36 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/variant/pull/27#pullrequestreview-13829714  

📁 test/Jamfile.v2

```diff
  48 |     ]
  49 |     [ run recursive_variant_test.cpp : : : <rtti>off <define>BOOST_NO_RTTI <define>BOOST_NO_TYPEID : variant_no_rtti_test ]
  50 |+     [ run hash_recursive_variant_test.cpp : : : <cxxflags>-std=c++11 : recursive_variant__hash_test ]
```

> Username: apolukhin  
> Created_at: 2016-12-20 19:14:11 UTC  
> Updated_at: 2016-12-21 11:27:28 UTC  
> Url: https://github.com/boostorg/variant/pull/27#discussion_r93304831  

You do not need the `: : : <cxxflags>-std=c++11 : recursive_variant__hash_test `. Flag `-std=c++11` is not unified across platforms and, for example, the MSVC compiler will fail to compile this test because of the  `-std=c++11`

> Username: very-cool-name  
> Created_at: 2016-12-20 19:29:29 UTC  
> Updated_at: 2016-12-21 11:27:28 UTC  
> Url: https://github.com/boostorg/variant/pull/27#discussion_r93307717  

How can I disable non c++11 compilers? It will solve both this and later issue with including unorder_set.

> Username: apolukhin  
> Created_at: 2016-12-21 06:18:14 UTC  
> Updated_at: 2016-12-21 11:27:28 UTC  
> Url: https://github.com/boostorg/variant/pull/27#discussion_r93380520  

Remove `<cxxflags>-std=c++11` and surround all the code in test with ifdefs:  
```  
#include <boost/config.hpp>  
  
#if !defined(BOOST_NO_CXX11_TEMPLATE_ALIASES) && !defined(BOOST_NO_CXX11_HDR_UNORDERED_SET)  
// stuff for tests and the run() function  
#else  
void run() {}  
#endif  
  
int test_main(int , char* [])  
{  
   run();  
   return 0;  
}  
```  
Macros are described here: http://www.boost.org/doc/libs/1_61_0/libs/config/doc/html/boost_config/boost_macro_reference.html#boost_config.boost_macro_reference.macros_that_describe_c__11_features_not_supported  
  
  
Using that approach the Boost.Config will help to decide to compile test or not. It is a more portable solution than the  `<cxxflags>-std=c++11` in Jamfile.  
  
Now if user runs `./b2 cxxflags="-std=c++03"` the test will compile and do nothing. If user runs `./b2 cxxflags="-std=c++14"` the test will compile and will actually do some testing.  
  
P.S.: there's a way to do the same thing using the Jamfile, but I'd rather avoid it. Jamfiles are not easy to support and are hard to understand. If I'm not mistaken   
```  
[ run hash_recursive_variant_test.cpp : : : <cxxflags>-std=c++11 : recursive_variant__hash_test ]  
```   
will append `-std=c++11` to compilation line,   
```  
[ run hash_recursive_variant_test.cpp : : <cxxflags>-std=c++11 : : recursive_variant__hash_test ]  
```  
will compile the test only if user compiles with `-std=c++11` parameter (`-std=c++14` and `-std=c++17` won't match).


📁 test/hash_recursive_variant_test.cpp

```diff
   6 |+ // http://www.boost.org/LICENSE_1_0.txt)
   7 |+ 
   8 |+ #include <unordered_set>
```

> Username: apolukhin  
> Created_at: 2016-12-20 19:15:25 UTC  
> Updated_at: 2016-12-21 11:27:28 UTC  
> Url: https://github.com/boostorg/variant/pull/27#discussion_r93305053  

Use macro from Boost.Config or just make sure that compiler is c++11 and disable the tests if it is not.

---

📁 test/hash_recursive_variant_test.cpp

```diff
   1 |+ // Copyright (c) 2011-2014
   2 |+ // Antony Polukhin
```

> Username: apolukhin  
> Created_at: 2016-12-20 19:17:09 UTC  
> Updated_at: 2016-12-21 11:27:28 UTC  
> Url: https://github.com/boostorg/variant/pull/27#discussion_r93305349  

Put your name in here, instead of mine :)  
Also change the copyright date to 2016

---

📁 test/hash_recursive_variant_test.cpp

```diff
  10 |+ #include "boost/test/minimal.hpp"
  11 |+ #include "boost/variant.hpp"
  12 |+ 
```

> Username: apolukhin  
> Created_at: 2016-12-20 19:18:27 UTC  
> Updated_at: 2016-12-21 11:27:28 UTC  
> Url: https://github.com/boostorg/variant/pull/27#discussion_r93305596  

Add links to the issue right into the code.


---

## Comment 2

> Username: apolukhin  
> Created_at: 2016-12-21 19:56:50 UTC  
> Url: https://github.com/boostorg/variant/pull/27#issuecomment-268623467  

Thanks a lot!

---
