# #367 - Investigate a synchronized container class [Open]

> Username: kylelutz  
> Created at: 2014-12-29 18:31:49 UTC  
> Updated at: 2017-03-21 04:14:53 UTC  
> Url: https://github.com/boostorg/compute/issues/367  

Investigate a synchronized container class which can be used from both the host and the device and automatically synchronizes the underlying data when necessary. This should offer an interface similar to `vector<T>` and probably be built around an OpenCL buffer allocated with `CL_MEM_ALLOC_HOST_PTR`.  
##
