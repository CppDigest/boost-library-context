# #831 Allow attaching user's own OpenCL context to Boost.Compute [Closed]

> Username: rosenrodt  
> Created at: 2019-04-06 07:05:43 UTC  
> Updated at: 2019-04-21 09:16:35 UTC  
> Closed at: 2019-04-21 09:16:34 UTC  
> Url: https://github.com/boostorg/compute/pull/831  

This PR allows user to attach their own OpenCL context via `default_context(context* = 0)`, with such benefits as described in #827. It adheres to `default_context()` behavior defined in boost/compute/system.hpp :  
  
> The default context is created once on the first time this function is called. Calling this function multiple times will always result in the same context object being returned.  
  
Now user can either let Boost.Compute decide which context to initialize with:  
```c++  
MyLib::MyLib(context ctx)  
{  
    boost::compute::default_context(); // optional  
}  
```  
Or attach their own context:  
```c++  
MyLib::MyLib(context ctx)  
{  
    boost::compute::default_context(&ctx);  
}  
```  
  
Test result of repeatedly attaching user context concurrently will correctly return only one unique context. See `test_user_context_thread_safety.cpp`:  
```  
$ ctest -C Release -R \w*user_default_context_thread_safety --repeat-until-fail 512  
  
1/1 Test #16:   
Start 16: core.user_default_context_thread_safety  
core.user_default_context_thread_safety ...   Passed    0.06 sec  
# … repeat 512 times  
  
100% tests passed, 0 tests failed out of 1  
  
Total Test time (real) =  31.65 sec  
```  
Tests passed with the following flags:  
BOOST_COMPUTE_THREAD_SAFE=ON, BOOST_COMPUTE_USE_CPP11=ON  
BOOST_COMPUTE_THREAD_SAFE=ON, BOOST_COMPUTE_USE_CPP11=OFF

---

## Comment 1

> Username: rosenrodt  
> Created_at: 2019-04-21 09:16:34 UTC  
> Url: https://github.com/boostorg/compute/pull/831#issuecomment-485237233  

I am closing this while preparing to open another PR

---
