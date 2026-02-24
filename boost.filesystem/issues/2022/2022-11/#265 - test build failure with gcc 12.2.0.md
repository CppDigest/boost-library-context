# #265 - test build failure with gcc 12.2.0 [Closed]

> Username: sgn  
> Created at: 2022-11-29 09:34:08 UTC  
> Updated at: 2022-11-29 11:24:44 UTC  
> Closed at: 2022-11-29 11:24:44 UTC  
> Url: https://github.com/boostorg/filesystem/issues/265  

Boost.Filesystem test failed to be built with gcc-12.2.0.  
Checked with boost 1.81.0 beta 1  
```  
In file included from libs/filesystem/test/operations_test.cpp:30:                                                                                                             
libs/filesystem/test/operations_test.cpp: In function 'void {anonymous}::directory_iterator_tests()':                                                                          
libs/filesystem/test/operations_test.cpp:610:33: error: 'const class boost::iterators::detail::postfix_increment_dereference_proxy<boost::filesystem::directory_entry>' has n  
o member named 'path'                                                                                                                                                          
  610 |         BOOST_TEST((*dir_itr++).path() == p);                                                                                                                          
      |                                 ^~~~                                                                                                                                   
./boost/core/lightweight_test.hpp:535:107: note: in definition of macro 'BOOST_TEST'                                                                                           
  535 | #define BOOST_TEST(expr) ( ::boost::detail::test_impl(#expr, __FILE__, __LINE__, BOOST_CURRENT_FUNCTION, (expr)? true: false) )                                        
      |                                                                                                           ^~~~                                                         
libs/filesystem/test/operations_test.cpp:619:38: error: 'const class boost::iterators::detail::postfix_increment_dereference_proxy<boost::filesystem::directory_entry>' has n  
o member named 'path'                                                                                                                                                          
  619 |         const fs::path p1 = (*itx++).path();                                                                                                                           
      |                                      ^~~~                                                                                                                              
libs/filesystem/test/operations_test.cpp:622:38: error: 'const class boost::iterators::detail::postfix_increment_dereference_proxy<boost::filesystem::directory_entry>' has n  
o member named 'path'                                                                                                                                                          
  622 |         const fs::path p2 = (*itx++).path();                                                                                                                           
      |                                      ^~~~                                                                                                                              
                                                                                                                                                                               
    "g++"   -fvisibility-inlines-hidden -O2 -g -pipe -D_FORTIFY_SOURCE=2 -fstack-protector-strong -m64 -pthread -O0 -fno-inline -Wall -g -fvisibility=hidden -fstack-protecto  
r-strong -D_FORTIFY_SOURCE=2 -march=native -mtune=native -pipe -O2 -g  -DBOOST_ALL_NO_LIB=1 -DBOOST_ATOMIC_STATIC_LINK=1 -DBOOST_FILESYSTEM_STATIC_LINK=1 -DBOOST_FILESYSTEM_  
VERSION=4  -I"." -I"libs/filesystem/test"  -c -o "bin.v2/libs/filesystem/test/operations_test_static.test/gcc-12/debug/link-static/threading-multi/visibility-hidden/operatio  
ns_test.o" "libs/filesystem/test/operations_test.cpp"    
```

---

## Comment 1

> Username: Lastique  
> Created at: 2022-11-29 11:24:44 UTC  
> Url: https://github.com/boostorg/filesystem/issues/265#issuecomment-1330479014  

Already fixed in [Boost.Iterator](https://github.com/boostorg/iterator/commit/7c9b4296a189f71b28de3aad025b08da1f4ca84b).
