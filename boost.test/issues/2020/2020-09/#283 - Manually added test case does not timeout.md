# #283 - Manually added test case does not timeout [Open]

> Username: Lagrang3  
> Created at: 2020-09-15 20:51:16 UTC  
> Updated at: 2022-03-04 21:46:41 UTC  
> Url: https://github.com/boostorg/test/issues/283  

boost version: 1.73.0  
  
My project needs a test suite on which several functions with the same  
signature are called on the same list of datasets.  
I resolved that a test suite built this way (see code below) as seen  
on similar examples fits my scenario.  
However, manually added test cases do not abide to the timeout values assigned.  
  
This code shows a minimal working example:  
```  
#include <boost/test/unit_test.hpp>  
  
#include <chrono>  
#include <thread>  
  
using namespace boost::unit_test;  
using namespace boost;  
  
void test_f()  
{  
    using namespace std::chrono_literals;  
    std::this_thread::sleep_for(5s); // this function will run for 5 seconds  
}  
  
struct my_test_suite : public test_suite  
{  
    my_test_suite(): test_suite("A test suite")   
    {  
        auto *tc =  
            BOOST_TEST_CASE(test_f);  
            tc->p_timeout.value=1; // 1 second timeout  
        add(tc);   
    }  
};  
  
test_suite* init_unit_test_suite(int argc,char* argv[])  
{  
    framework::master_test_suite().p_name.value = "my UT suite";  
    framework::master_test_suite().add( new my_test_suite  );  
    return 0;  
}  
```  
  
Expected: failure of the test for timeout.  
Outcome: all test cases pass.
