# #443 - How to set test timeout dynamically ? [Open]

> Username: redboltz  
> Created at: 2025-03-17 03:04:57 UTC  
> Updated at: 2025-03-17 03:21:41 UTC  
> Url: https://github.com/boostorg/test/issues/443  

I am using Boost.Test version 1.87.0.    
I am considering setting a uniform timeout for test cases. The purpose is to terminate a test case if it gets stuck due to some issue and proceed to the next test case.    
  
However, I want to disable this timeout when debugging with a debugger such as `gdb`, as timeouts during debugging can interfere with the process.    
  
According to the documentation:    
<https://www.boost.org/doc/libs/1_87_0/libs/test/doc/html/boost_test/testing_tools/timeout.html>    
it appears that timeouts can be specified using the described method.    
  
First, I specified a timeout using a constant, following the example in the documentation.    
  
- **Passing the test timeout as a command-line argument**    
- **Example using a compile-time constant**    
  <https://godbolt.org/z/9fneKoTvj>    
  
This resulted in the expected timeout.    
However, to disable the timeout for debugging, recompilation is required.    
  
Next, I attempted to specify a variable instead of a compile-time constant.    
  
- **Example using a runtime-determined variable**    
  <https://godbolt.org/z/GjzMrjPhG>    
  
This also resulted in the expected timeout.    
  
To dynamically switch the timeout on or off and adjust its duration, I considered passing it as a command-line argument.    
  
```cpp  
inline unsigned long get_timeout() {  
    std::cout << "In get_timeout(), argc:" << boost::unit_test::framework::master_test_suite().argc << std::endl;  
    if (boost::unit_test::framework::master_test_suite().argc > 1) {  
        return  
            std::stoul(  
                boost::unit_test::framework::master_test_suite().argv[1]  
            );  
    }  
    return 0;  
}  
```  
  
- **Example retrieving the value from a command-line argument**    
  <https://godbolt.org/z/6fTPjWbxj>    
  
However, in this case, the timeout did not take effect.    
  
Upon analysis, it appears that:  
  
```cpp  
BOOST_AUTO_TEST_CASE(t1, * boost::unit_test::timeout(get_timeout())) {  
```  
  
calls `get_timeout()`, but at this point,    
`boost::unit_test::framework::master_test_suite().argc` and    
`boost::unit_test::framework::master_test_suite().argv`    
have not yet been initialized.    
  
To confirm this, I added the following code inside the test case:  
  
```cpp  
auto tout = get_timeout();  
std::cout   
    << "In test case argc: "  
    << boost::unit_test::framework::master_test_suite().argc   
    << " result of get_timeout(): "  
    << tout  
    << std::endl;  
```  
  
At this timing, `argc` and `argv` were correctly retrieved.    
  
Is there a good way to dynamically switch the timeout?
