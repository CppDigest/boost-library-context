# #818 - Failing unit tests related to clEnqueueSVMMemcpy on Intel GPU [Open]

> Username: Kristian-Popov  
> Created at: 2019-01-24 21:28:07 UTC  
> Updated at: 2019-01-24 21:28:07 UTC  
> Url: https://github.com/boostorg/compute/issues/818  

To whom it may concern,  
  
Some of unit tests fail on my setup, all failing tests are related to command_queue::enqueue_svm_memcpy() function. First GPU in my system (and default device for unit tests) is Intel(R) HD Graphics 630, I guess problems with implementation of clEnqueueSVMMemcpy are known (looking at this Twitter message by @jszuppe https://twitter.com/jszpp/status/736564025748795392 ). Unit tests related to that function are skipped using bug_in_svmmemcpy function, but it skips only on non-Windows OS's and AMD devices. Is there any reason why it is not skipped on Intel GPUs?  
Detailed log of failing tests:  
  
> test_copy.exe  
Running 15 test cases...  
C:\Users\chris-pet-projects\Documents\boost-compute\include\boost/compute/command_queue.hpp(1693): fatal error: in "class boost::compute::event __cdecl boost::compute::command_queue::enqueue_svm_memcpy(void *,const void *,unsigned __int64,const class boost::compute::wait_list &)": class boost::exception_detail::clone_impl<struct boost::exception_detail::error_info_injector<class boost::compute::opencl_error> >: Invalid Value  
C:\Users\chris-pet-projects\Documents\boost-compute\test\test_copy.cpp(341): last checkpoint: "copy_svm_ptr" test entry  
C:\Users\chris-pet-projects\Documents\boost-compute\include\boost/compute/command_queue.hpp(1724): fatal error: in "class boost::compute::event __cdecl boost::compute::command_queue::enqueue_svm_memcpy_async(void *,const void *,unsigned __int64,const class boost::compute::wait_list &)": class boost::exception_detail::clone_impl<struct boost::exception_detail::error_info_injector<class boost::compute::opencl_error> >: Invalid Value  
C:\Users\chris-pet-projects\Documents\boost-compute\test\test_copy.cpp(364): last checkpoint: "copy_async_svm_ptr" test entry  
  
By some reason clEnqueueSVMMemcpy  returns INVALID_VALUE although all parameters appears to be valid.  
  
When I force unit tests to use CPU `set "BOOST_COMPUTE_DEFAULT_DEVICE_TYPE=CPU" && ctest` all unit tests pass.  
Related to #528
