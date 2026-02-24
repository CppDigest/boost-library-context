# #713 - Support OpenCL 2.1 API [Closed]

> Username: jszuppe  
> Created at: 2017-04-24 16:54:25 UTC  
> Updated at: 2017-06-11 20:21:19 UTC  
> Closed at: 2017-06-11 20:21:19 UTC  
> Url: https://github.com/boostorg/compute/issues/713  

### Summary of changes from OpenCL 2.0 to OpenCL 2.1:  
The following features are added to the OpenCL 2.1 platform layer and runtime (sections 4 and 5):  
- [x] `clGetKernelSubGroupInfo` API call. (`kernel`)  
- [x] `CL_KERNEL_MAX_NUM_SUB_GROUPS`, `CL_KERNEL_COMPILE_NUM_SUB_GROUPS` additions to table 5.21 of the API specification.  (`kernel`)  
- [x] Added table ~5.22~ 5.21 to the API specification with the enums: `CL_KERNEL_MAX_SUB_GROUP_SIZE_FOR_NDRANGE`, `CL_KERNEL_SUB_GROUP_COUNT_FOR_NDRANGE` and `CL_KERNEL_LOCAL_SIZE_FOR_SUB_GROUP_COUNT` (`kernel`)  
- [x] `clCloneKernel` API call. (`kernel`)  
- [x] `clCreateProgramWithIL` API call.  (`program`)  
- [x] `CL_PROGRAM_IL` to table 5.17 of the API specification. (`program`)  
- [x] `clGetHostTimer` and `clGetDeviceAndHostTimer` API calls. (`device`)  
- [x]  `CL_DEVICE_IL_VERSION`, `CL_DEVICE_MAX_NUM_SUB_GROUPS`, `CL_DEVICE_SUB_GROUP_INDEPENDENT_FORWARD_PROGRESS` added to table 4.3 of the API specification. (`device`)  
- [x] `clEnqueueSVMMigrateMem` API call. (`command_queue`)  
- [x] `clSetDefaultDeviceCommandQueue` API call. (`command_queue`, consider new `device_queue` class)  
- [x] `CL_QUEUE_DEVICE_DEFAULT` added to table 5.2 of the API specification. (`command_queue`)  
- [x] `CL_PLATFORM_HOST_TIMER_RESOLUTION` added to table 4.1 of the API specification. (`platform`)

---

## Comment 1

> Username: jszuppe  
> Created at: 2017-06-11 20:21:18 UTC  
> Url: https://github.com/boostorg/compute/issues/713#issuecomment-307654201  

Done.
