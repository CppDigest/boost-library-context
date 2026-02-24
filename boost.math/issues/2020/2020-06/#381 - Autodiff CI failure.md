# #381 - Autodiff CI failure [Closed]

> Username: NAThompson  
> Created at: 2020-06-28 14:16:08 UTC  
> Updated at: 2024-01-24 21:41:17 UTC  
> Closed at: 2024-01-24 21:41:17 UTC  
> Url: https://github.com/boostorg/math/issues/381  

In [this](https://ci.appveyor.com/project/jzmaddock/math/builds/33777945/job/v018io41piqbnur5?fullLog=true) build we have the following failure:  
  
```  
testing.capture-output ..\..\..\bin.v2\libs\math\test\test_autodiff_8.test\9e4fa9cecbbb3b46f8eab5b212bfeba6\test_autodiff_8.run  
====== BEGIN OUTPUT ======  
Running 71 test cases...  
test_autodiff_8.cpp(100): error: in "test_autodiff_8/jacobi_elliptic_hpp<float>": check isNearZero( boost::math::jacobi_ns(make_fvar<T, m>(k), make_fvar<T, m>(theta)) .derivative(0u) - boost::math::jacobi_ns(k, theta)) has failed  
*** 1 failure is detected in the test module "test_autodiff"  
   
EXIT STATUS: 201   
====== END OUTPUT ======  
```
