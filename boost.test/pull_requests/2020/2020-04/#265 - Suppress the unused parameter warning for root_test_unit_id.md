# #265 Suppress the unused parameter warning for root_test_unit_id [Merged]

> Username: ppmfloss  
> Created at: 2020-04-17 19:07:53 UTC  
> Updated at: 2020-04-21 14:23:42 UTC  
> Merged at: 2020-04-19 20:12:55 UTC  
> Closed at: 2020-04-19 20:12:55 UTC  
> Url: https://github.com/boostorg/test/pull/265  

The parameter ```root_test_unit_id``` in the method ```test_start``` in the file ```boost/test/tree/observer.hpp``` causes unused parameter warning.  
```  
// a.cc  
#define BOOST_TEST_MODULE your_test_module  
#include <boost/test/included/unit_test.hpp>  
  
BOOST_AUTO_TEST_CASE( test1 )  
{  
    // reports 'error in "test1": test 2 == 1 failed'  
    BOOST_TEST( 2 == 1 );  
}  
```  
```  
g++ -Wall -Wextra -std=c++17 -g -O0 a.cc -I/home/ppm/src/boost -o a  
In file included from /home/ppm/src/boost/boost/test/results_collector.hpp:19,  
                 from /home/ppm/src/boost/boost/test/impl/junit_log_formatter.ipp:27,  
                 from /home/ppm/src/boost/boost/test/included/unit_test.hpp:19,  
                 from a.cc:6:  
/home/ppm/src/boost/boost/test/tree/observer.hpp: In member function ‘virtual void boost::unit_test::test_observer::test_start(boost::unit_test::counter_t, boost::unit_test::test_unit_id)’:  
/home/ppm/src/boost/boost/test/tree/observer.hpp:48:84: warning: unused parameter ‘root_test_unit_id’ [-Wunused-parameter]  
    virtual void    test_start( counter_t /* number_of_test_cases */, test_unit_id root_test_unit_id ) {}  
                                                                      ~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~  
```  
This PR will fix it.

---
