# #793 - Calling the method with value instead of a pointer (local_work_size[0]) for version 2.0 of OpenCL [Closed]

> Username: rrajpaul  
> Created at: 2018-08-14 18:29:34 UTC  
> Updated at: 2018-08-25 01:38:55 UTC  
> Closed at: 2018-08-25 01:38:55 UTC  
> Url: https://github.com/boostorg/compute/issues/793  

Please the error information below - I am compiling against OpenCL 2.0  
  
/home/ryankr/repository/compute/test/test_kernel.cpp:201:5:   required from here  
/home/ryankr/repository/compute/include/boost/compute/detail/get_object_info.hpp:46:9: error: invalid conversion from ‘size_t {aka long unsigned int}’ to ‘long unsigned int*’ [-fpermissive]  
         );  
         ^

---

## Comment 1

> Username: rrajpaul  
> Created at: 2018-08-25 01:38:51 UTC  
> Url: https://github.com/boostorg/compute/issues/793#issuecomment-415921599  

This issue is not a real issue it occurs when the user environment is corrupted. I re-installed Linux mint to version 19 and did a git clone and compiled without any errors. Please close this ticket
