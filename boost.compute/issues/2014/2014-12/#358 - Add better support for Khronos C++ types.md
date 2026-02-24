# #358 - Add better support for Khronos C++ types [Open]

> Username: kylelutz  
> Created at: 2014-12-29 17:55:06 UTC  
> Updated at: 2018-05-16 22:21:34 UTC  
> Url: https://github.com/boostorg/compute/issues/358  

Add better support for interoperating between Boost.Compute and the Khronos OpenCL C++ wrapper types. For example, the following should be possible:  
  
```  
cl::CommandQueue q1 = ...;  
boost::compute::command_queue q2 = q1;  
```  
##

---

## Comment 1

> Username: maddanio  
> Created at: 2018-05-16 22:21:34 UTC  
> Url: https://github.com/boostorg/compute/issues/358#issuecomment-389684443  

shouldn't it be easy to interoperate via the c-api level? i feel such wrappers should be quite trivial, but maybe i am wrong, maybe ownership is complicated?
