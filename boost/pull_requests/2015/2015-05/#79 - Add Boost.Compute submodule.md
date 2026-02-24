# #79 Add Boost.Compute submodule. [Merged]

> Username: kylelutz  
> Created at: 2015-05-03 23:18:53 UTC  
> Updated at: 2015-05-07 04:55:56 UTC  
> Merged at: 2015-05-04 19:07:35 UTC  
> Closed at: 2015-05-04 19:07:35 UTC  
> Url: https://github.com/boostorg/boost/pull/79  



---

## Comment 1

> Username: danieljames  
> Created_at: 2015-05-04 19:07:23 UTC  
> Url: https://github.com/boostorg/boost/pull/79#issuecomment-98817650  

This is master, but we use develop on the develop branch. Not really a problem, next time you push a change to develop the bot will update the super project to use that.  
  
Also, is this ready to be added to the unit tests? If you want, I'll add it to status/Jamfile.v2 which is used to run the tests.

---

## Comment 2

> Username: kylelutz  
> Created_at: 2015-05-05 03:45:58 UTC  
> Url: https://github.com/boostorg/boost/pull/79#issuecomment-98933159  

This should be ready to add to the test machines. One caveat however, the Boost.Compute unit tests assume an OpenCL implementation is available on the system (i.e. `CL/cl.h` and `libOpenCL.so` are present). I'm not sure the best way to instruct `b2` and the regression-testing framework to only compile/run these tests on OpenCL capable machines.

---
