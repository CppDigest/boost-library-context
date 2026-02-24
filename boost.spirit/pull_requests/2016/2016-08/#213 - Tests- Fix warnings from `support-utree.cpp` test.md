# #213 Tests: Fix warnings from `support/utree.cpp` test [Merged]

> Username: Kojoley  
> Created at: 2016-08-25 14:44:49 UTC  
> Updated at: 2017-11-12 22:01:30 UTC  
> Merged at: 2016-08-25 21:51:13 UTC  
> Closed at: 2016-08-25 21:51:13 UTC  
> Url: https://github.com/boostorg/spirit/pull/213  

Fixes following warnings:  
  
```  
gcc.compile.c++ ../../../bin.v2/libs/spirit/test/support_utree-p3.test/gcc-5/debug/coverage-on/support/utree.o  
In file included from ../../../boost/detail/lightweight_test.hpp:15:0,  
                 from support/utree.cpp:11:  
support/utree.cpp: In function ‘int main()’:  
support/utree.cpp:351:35: warning: logical not is only applied to the left hand side of comparison [-Wlogical-not-parentheses]  
         BOOST_TEST(!utree(true)   == utree(false));  
                                   ^  
../../../boost/core/lightweight_test.hpp:146:28: note: in definition of macro ‘BOOST_TEST’  
 #define BOOST_TEST(expr) ((expr)? (void)0: ::boost::detail::test_failed_impl(#expr, __FILE__, __LINE__, BOOST_CURRENT_FUNCTION))  
                            ^  
support/utree.cpp:352:35: warning: logical not is only applied to the left hand side of comparison [-Wlogical-not-parentheses]  
         BOOST_TEST(!utree(false)  == utree(true));  
                                   ^  
../../../boost/core/lightweight_test.hpp:146:28: note: in definition of macro ‘BOOST_TEST’  
 #define BOOST_TEST(expr) ((expr)? (void)0: ::boost::detail::test_failed_impl(#expr, __FILE__, __LINE__, BOOST_CURRENT_FUNCTION))  
                            ^  
support/utree.cpp:353:35: warning: logical not is only applied to the left hand side of comparison [-Wlogical-not-parentheses]  
         BOOST_TEST(!utree(1)      == utree(false));  
                                   ^  
../../../boost/core/lightweight_test.hpp:146:28: note: in definition of macro ‘BOOST_TEST’  
 #define BOOST_TEST(expr) ((expr)? (void)0: ::boost::detail::test_failed_impl(#expr, __FILE__, __LINE__, BOOST_CURRENT_FUNCTION))  
                            ^  
support/utree.cpp:354:35: warning: logical not is only applied to the left hand side of comparison [-Wlogical-not-parentheses]  
         BOOST_TEST(!utree(0)      == utree(true));  
                                   ^  
../../../boost/core/lightweight_test.hpp:146:28: note: in definition of macro ‘BOOST_TEST’  
 #define BOOST_TEST(expr) ((expr)? (void)0: ::boost::detail::test_failed_impl(#expr, __FILE__, __LINE__, BOOST_CURRENT_FUNCTION))  
                            ^  
```

---
