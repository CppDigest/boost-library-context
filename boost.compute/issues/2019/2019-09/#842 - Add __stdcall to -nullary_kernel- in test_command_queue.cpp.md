# #842 - Add __stdcall to "nullary_kernel" in test_command_queue.cpp [Open]

> Username: yuxianch  
> Created at: 2019-09-24 07:09:44 UTC  
> Updated at: 2019-09-24 07:09:44 UTC  
> Url: https://github.com/boostorg/compute/issues/842  

In windows, there is an error when compiling test_command_queue.cpp for mismatch convension.  
  
> test_command_queue.cpp:232:11: error: no matching member function for call to 'enqueue_native_kernel'  
    queue.enqueue_native_kernel(&nullary_kernel);  
    ~~~~~~^~~~~~~~~~~~~~~~~~~~~  
../../..\boost/compute/command_queue.hpp:1534:11: note: candidate function not viable: no known conversion from 'void (*)()' to 'void (*)() __attribute__((stdcall))' for 1st argument  
    event enqueue_native_kernel(void (BOOST_COMPUTE_CL_CALLBACK *user_func)(void),  
          ^  
../../..\boost/compute/command_queue.hpp:1502:11: note: candidate function not viable: requires at least 6 arguments, but 1 was provided  
    event enqueue_native_kernel(void (BOOST_COMPUTE_CL_CALLBACK *user_func)(void *),  
          ^  
  
After changing code in test_command_queue.cpp from "static void nullary_kernel()" to "static __stdcall void nullary_kernel()", above error became gone.  
This issue is similar with [#36](https://github.com/boostorg/compute/issues/36).  
Please help to fix.
