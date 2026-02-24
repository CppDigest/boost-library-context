# #134 Multi thread Example [Closed]

> Username: Mageswaran1989  
> Created at: 2014-06-09 13:51:39 UTC  
> Updated at: 2014-07-02 19:47:24 UTC  
> Closed at: 2014-06-10 10:26:32 UTC  
> Url: https://github.com/boostorg/compute/pull/134  

Hi,  
Added example multi_thread & visualize_kernel.  
Request to review the multi thread example.  
I am new to writing CMake file, added few stuff to example/CMakeLists.txt and made to compile if   
BOOST_COMPUTE_USE_OFFLINE_CACHE is enabled.  
  
Fixed warning in opencv_optical_flow_camera.  
  
Modified matrix_transpose example, such that the matrix size can be controlled from command line.  
When I try with bigger matrix I get following error  
$ ./matrix_transpose 8 8  
Float Matrix Size : 8192x8192  
Size in MB : 256  
Grid Size  : 256x256 blocks  
Local Size : 32x8 threads  
  
Copy_Kernel  
terminate called after throwing an instance of 'boost::exception_detail::clone_implboost::exception_detail::error_info_injector<boost::compute::context_error >'  
  what():  CL_MEM_OBJECT_ALLOCATION_FAILURE error executing CL_COMMAND_NDRANGE_KERNEL on GeForce GT 640 (Device 0).  
  
Aborted (core dumped)  
#   
  
./matrix_transpose 20 20  
Float Matrix Size : 20480x20480  
Size in MB : 1600  
Grid Size  : 640x640 blocks  
Local Size : 32x8 threads  
  
terminate called after throwing an instance of 'std::bad_alloc'  
  what():  std::bad_alloc  
Aborted (core dumped)

---

## Comment 1

> Username: Mageswaran1989  
> Created_at: 2014-06-10 10:27:04 UTC  
> Url: https://github.com/boostorg/compute/pull/134#issuecomment-45597235  

A new pull request is been submitted

---
