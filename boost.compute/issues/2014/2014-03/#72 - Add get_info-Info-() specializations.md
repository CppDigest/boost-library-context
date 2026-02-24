# #72 - Add get_info<Info>() specializations [Closed]

> Username: kylelutz  
> Created at: 2014-03-19 02:15:14 UTC  
> Updated at: 2014-07-08 05:56:33 UTC  
> Closed at: 2014-07-08 05:56:33 UTC  
> Url: https://github.com/boostorg/compute/issues/72  

Right now, the `get_info<T>(enum)` function allows users to retrieve values from the corresponding `clGet*Info()` functions.  
  
However, the current API requires users to specify both the enum name (e.g. `CL_DEVICE_LOCAL_MEM_SIZE`) and the value type (e.g. `cl_ulong`).  
  
We should add specializations for the known enum types with hard-coded return types. For example, the following test should pass:  
  
```  
device.get_info<CL_DEVICE_LOCAL_MEM_SIZE>() == device.get_info<cl_ulong>(CL_DEVICE_LOCAL_MEM_SIZE);  
```  
  
Care should be taken to ensure that only enum types available from the OpenCL version reported by `cl.h` are used. For example, don't try to use info enums from OpenCL 1.2 like `CL_DEVICE_PRINTF_BUFFER_SIZE` if `CL_VERSION_1_2` is not defined.

---

## Comment 1

> Username: kylelutz  
> Created at: 2014-07-03 19:23:55 UTC  
> Updated at: 2014-07-08 05:56:31 UTC  
> Url: https://github.com/boostorg/compute/issues/72#issuecomment-47973629  

Implemented in PR #176
