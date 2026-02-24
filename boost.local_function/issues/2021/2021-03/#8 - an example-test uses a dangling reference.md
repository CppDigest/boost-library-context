# #8 - an example/test uses a dangling reference [Open]

> Username: jhcarl0814  
> Created at: 2021-03-12 13:22:05 UTC  
> Updated at: 2021-03-12 13:22:05 UTC  
> Url: https://github.com/boostorg/local_function/issues/8  

libs/local_function/test/return_assign.cpp uses a dangling reference:    
```c++  
#include <boost/local_function.hpp>  
#include <boost/function.hpp>  
#include <boost/detail/lightweight_test.hpp>  
#include <iostream>  
  
//[return_assign  
void call1(boost::function<int (int) > f) { BOOST_TEST(f(1) == 5); }  
void call0(boost::function<int (void)> f) { BOOST_TEST(f() == 5); }  
  
boost::function<int (int, int)> linear(const int& slope) {  
    int BOOST_LOCAL_FUNCTION(const bind& slope,  
            int x, default 1, int y, default 2) {  
        return x + slope * y;  
    } BOOST_LOCAL_FUNCTION_NAME(lin)  
  
    boost::function<int (int, int)> f = lin; // Assign to local variable.  
    BOOST_TEST(f(1, 2) == 5);  
  
    call1(lin); // Pass to other functions.  
    call0(lin);  
  
    return lin; // Return.  
}  
  
void call(void) {  
    boost::function<int (int, int)> f = linear(2); // create temporary, bind reference to temporary  
    BOOST_TEST(f(1, 2) == 5); // !!!!!!!!!!!!!!!!!!!!!!!! use the reference  
}  
//]  
  
int main(void) {  
    call();  
    return boost::report_errors();  
}  
```  
changing the target from temporary to local variable can let the test fail:    
```c++  
#include <boost/local_function.hpp>  
#include <boost/function.hpp>  
#include <boost/detail/lightweight_test.hpp>  
#include <iostream>  
  
//[return_assign  
void call1(boost::function<int(int) > f) { BOOST_TEST(f(1) == 5); }  
void call0(boost::function<int(void)> f) { BOOST_TEST(f() == 5); }  
  
boost::function<int(int, int)> linear(const int& slope) {  
    int BOOST_LOCAL_FUNCTION(const bind & slope,  
        int x, default 1, int y, default 2) {  
        return x + slope * y;  
    } BOOST_LOCAL_FUNCTION_NAME(lin)  
  
        boost::function<int(int, int)> f = lin; // Assign to local variable.  
    BOOST_TEST(f(1, 2) == 5);  
  
    call1(lin); // Pass to other functions.  
    call0(lin);  
  
    return lin; // Return.  
}  
  
void call1(void) {  
    boost::function<int(int, int)> f = linear(2);  
    BOOST_TEST(f(1, 2) == 5); // !!!!!!!!!!!!!!!!!!!!!!!! test passed, although dangling reference  
}  
void call2(void) {  
    int a = 2;  
    boost::function<int(int, int)> f = linear(a);  
    a = 3;  
    BOOST_TEST(f(1, 2) == 5); // !!!!!!!!!!!!!!!!!!!!!!!! test failed  
}  
//]  
  
int main(void) {  
    call1();  
    call2();  
    return boost::report_errors();  
}  
```
