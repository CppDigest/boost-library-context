# #475 - No regression testing against NVCC compiler [Closed]

> Username: ghost  
> Created at: 2021-02-10 15:20:22 UTC  
> Updated at: 2021-03-08 18:58:42 UTC  
> Closed at: 2021-03-08 18:58:42 UTC  
> Url: https://github.com/boostorg/boost/issues/475  

We have an application involving realtime processing that uses boost and CUDA. CUDA is a great way to leverage the computing power of GPUs for numerical calculations. I have found that boost and CUDA do not play well together resulting in warnings and sometimes in build issues. I now realize that this is likely because there is not regression testing on the NVCC compilers from nvidia. Is there any interest in running these tests or is ours a completely unique use case?

---

## Comment 1

> Username: mclow  
> Created at: 2021-03-08 18:58:42 UTC  
> Url: https://github.com/boostorg/boost/issues/475#issuecomment-792993521  

All of our testing is done on a volunteer basis. I'm sure the library authors would welcome someone running an NVCC testing instance.   Docs are here: https://www.boost.org/development/running_regression_tests.html
