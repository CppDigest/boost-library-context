# #194 - "master_test_suite" declared twice [Closed]

> Username: DesWurstes  
> Created at: 2019-01-01 16:43:24 UTC  
> Updated at: 2019-02-08 17:25:42 UTC  
> Closed at: 2019-02-08 13:43:04 UTC  
> Url: https://github.com/boostorg/test/issues/194  

```  
In file included from /usr/local/include/boost/test/tree/auto_registration.hpp:21,  
                 from /usr/local/include/boost/test/unit_test_suite.hpp:17,  
                 from /usr/local/include/boost/test/unit_test.hpp:19,  
                 from test/test_bitcoin_main.cpp:11:  
/usr/local/include/boost/test/tree/test_unit.hpp:46:38: warning: redundant redeclaration of 'boost::unit_test::master_test_suite_t& boost::unit_test::framework::master_test_suite()' in same scope [-Wredundant-decls]  
 BOOST_TEST_DECL master_test_suite_t& master_test_suite();  
                                      ^~~~~~~~~~~~~~~~~  
In file included from /usr/local/include/boost/test/unit_test_suite.hpp:16,  
                 from /usr/local/include/boost/test/unit_test.hpp:19,  
                 from test/test_bitcoin_main.cpp:11:  
/usr/local/include/boost/test/framework.hpp:195:38: note: previous declaration of 'boost::unit_test::master_test_suite_t& boost::unit_test::framework::master_test_suite()'  
 BOOST_TEST_DECL master_test_suite_t& master_test_suite();  
 ```  
  
Should I submit a patch?

---

## Comment 1

> Username: raffienficiaud  
> Created at: 2019-02-08 13:43:04 UTC  
> Url: https://github.com/boostorg/test/issues/194#issuecomment-461806653  

In master, closing. Thanks!
