# #288 - BOOST_<level>_NO_THROW with return value [Open]

> Username: redboltz  
> Created at: 2020-10-23 08:35:09 UTC  
> Updated at: 2020-10-23 08:35:09 UTC  
> Url: https://github.com/boostorg/test/issues/288  

I'm using BOOST_REQUIRE_NO_THROW() as follows:  
  
```cpp  
#define BOOST_TEST_MAIN  
#include <boost/test/included/unit_test.hpp>  
  
struct nc {  
    nc(int v): v { v } {}  
    nc() = delete;  
    nc(nc const&) = delete;  
    nc(nc&&) = default;  
    int v;  
};  
  
nc success() {  
    return nc(1);  
}  
  
nc fail() {  
    throw(42);  
    return nc(2);  
}  
  
BOOST_AUTO_TEST_CASE(test_case1) {  
    BOOST_REQUIRE_NO_THROW(  
        auto v1 = success();  
        std::cout << v1.v << std::endl;  
        auto v2 = fail();  
        std::cout << v2.v << std::endl;  
    );  
}  
```  
  
It works well. But output is not easy to understand.  
  
Here is runnable demo:   
https://wandbox.org/permlink/KnsKJ9xq9htug3iG  
  
The output is:  
  
```  
Running 1 test case...  
1  
prog.cc(27): fatal error: in "test_case1": unexpected exception thrown by auto v1 = success(); std::cout << v1.v << std::endl; auto v2 = fail(); std::cout << v2.v << std::endl;  
  
*** 1 failure is detected in the test module "Master Test Suite"  
```  
  
All lines in `BOOST_REQUIRE_NO_THROW()` seems to regard as one line.  
  
Unfortunately the following notation is not accepted.  
  
```cpp  
    BOOST_REQUIRE_NO_THROW(nc v1 = success());  
    std::cout << v1.v << std::endl; // no v1 in the scope  
```  
  
```cpp  
    nc v1 = BOOST_REQUIRE_NO_THROW(success()); // Syntax error  
    std::cout << v1.v << std::endl;  
```  
      
Finally I found a workaround using optional.  
  
```cpp  
#define BOOST_TEST_MAIN  
#include <boost/test/included/unit_test.hpp>  
#include <optional>  
  
struct nc {  
    nc(int v): v { v } {}  
    nc() = delete;  
    nc(nc const&) = delete;  
    nc(nc&&) = default;  
    int v;  
};  
  
nc success() {  
    return nc(1);  
}  
  
nc fail() {  
    throw(42);  
    return nc(2);  
}  
  
BOOST_AUTO_TEST_CASE(test_case1) {  
    std::optional<nc> v1;  
    BOOST_REQUIRE_NO_THROW(v1.emplace(success()));  
    std::cout << v1->v << std::endl;  
      
    std::optional<nc> v2;  
    BOOST_REQUIRE_NO_THROW(v2.emplace(fail()));  
    std::cout << v2->v << std::endl;  
}  
```  
  
It works well and the error is reported accurate line.  
  
Here is runnable demo:   
https://wandbox.org/permlink/gEYLIxgofWub2G0j  
  
The output is:  
  
```  
Running 1 test case...  
1  
prog.cc(28): fatal error: in "test_case1": unexpected exception thrown by v2.emplace(fail())  
  
*** 1 failure is detected in the test module "Master Test Suite"  
```  
  
I think that the optional workaround is acceptable.  
But if there is better way, I'd like to know.
