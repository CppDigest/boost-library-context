# #296 - Bug: free(): invalid next size (fast) on std::exit with report sink [Open]

> Username: gg-yb  
> Created at: 2021-01-06 11:54:56 UTC  
> Updated at: 2022-03-02 12:35:21 UTC  
> Url: https://github.com/boostorg/test/issues/296  

**Bug Information:**  
  
Introduced in commit 908368f279  
  
Confirmed to be present in `develop` as of 9d863d07  
  
**Steps to reproduce:**  
  
Use the following `main.cpp`:  
```cpp  
#define BOOST_TEST_MODULE Tests  
#define BOOST_TEST_NO_MAIN 1  
#define BOOST_TEST_ALTERNATIVE_INIT_API 1  
  
#include <boost/test/included/unit_test.hpp>  
  
int main( int argc, char** argv )  
{  
    boost::unit_test::unit_test_main( init_unit_test, argc, argv );  
}  
  
BOOST_AUTO_TEST_CASE( SpuriousDoubleFree )  
{  
    std::exit( 0 );  
}  
```  
  
Compile with `g++ -O0 -g3 -std=c++14 -o test ./main.cpp`  
  
Run with `./test --report_sink=/dev/null`  
  
Sample output:  
```  
$ ./test --report_sink=/dev/null  
Running 1 test case...  
free(): invalid next size (fast)  
Segmentation fault (core dumped)  
```  
  
**Rationale why this behavior is not expected:**  
  
Consider a module which checks some invariants not in the control of the test (e.g. network) and exits if those invariants can not be established. In this case, the test binary should not segfault. Abnormal program termination (which doesn't cause this behavior, as exit handlers are not called) is not an option in every scenario, e.g. consider a shell utility which depends on differentiated return codes.  
  
**Environment:**  
  
```  
$ g++ --version  
g++ (Ubuntu 7.5.0-3ubuntu1~18.04) 7.5.0  
Copyright (C) 2017 Free Software Foundation, Inc.  
This is free software; see the source for copying conditions.  There is NO  
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  
```
