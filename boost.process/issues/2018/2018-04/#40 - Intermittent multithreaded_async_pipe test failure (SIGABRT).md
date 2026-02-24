# #40 - Intermittent multithreaded_async_pipe test failure (SIGABRT) [Open]

> Username: phmccarty  
> Created at: 2018-04-25 23:04:30 UTC  
> Updated at: 2020-12-14 14:17:43 UTC  
> Url: https://github.com/boostorg/process/issues/40  

After building Boost 1.67.0 and running the test suite, I am running into an intermittent test failure for the multithreaded_async_pipe test case from test/async_pipe.cpp, where a SIGABRT is raised.  
  
* Build environment: Clear Linux OS, build 22070  
* GCC version: 7.3.0  
  
For reference, I am building Boost with the mock tool, using the spec file from https://github.com/clearlinux-pkgs/boost/blob/cbefce45d9e125337d0545ca4957c761d7fd58ec/boost.spec, modified for 1.67.0.  
   
When the test fails, the output from the async_pipe test binary is:  
  
```  
terminate called after throwing an instance of 'boost::exception_detail::clone_impl<boost::exception_detail::error_info_injector<boost::system::system_error> >'  
  what():  assign: Bad file descriptor  
Running 3 test cases...  
unknown location(0): fatal error: in "multithreaded_async_pipe": signal: SIGABRT (application abort requested)  
../libs/process/test/async_pipe.cpp(67): last checkpoint: "multithreaded_async_pipe" test entry  
  
*** 1 failure is detected in the test module "Master Test Suite"  
  
EXIT STATUS: 201  
```  
  
Running the test binary through GDB, I captured the backtraces for the crashing thread and the other threads. If you would like me to post the backtraces from the other threads, let me know.  
  
Backtrace from crashing thread:  
  
```  
#0  0x00007ffff748f247 in raise () from /usr/lib64/libc.so.6  
#1  0x00007ffff7490ba1 in abort () from /usr/lib64/libc.so.6  
#2  0x00007ffff78749fd in __gnu_cxx::__verbose_terminate_handler ()  
#3  0x00007ffff78726d6 in __cxxabiv1::__terminate (handler=<optimized out>)  
    at ../../../../gcc-7.3.0/libstdc++-v3/libsupc++/eh_terminate.cc:47  
#4  0x00007ffff7872721 in std::terminate ()  
    at ../../../../gcc-7.3.0/libstdc++-v3/libsupc++/eh_terminate.cc:57  
#5  0x00007ffff78a7b1e in std::execute_native_thread_routine (__p=0x749420)  
    at ../../../../../gcc-7.3.0/libstdc++-v3/src/c++11/thread.cc:91  
#6  0x00007ffff7b7a66a in ?? () from /usr/lib64/libpthread.so.0  
#7  0x00007ffff756fb3f in clone () from /usr/lib64/libc.so.6  
```  
  
If you require further information about the build environment or other details, I can provide them.

---

## Comment 1

> Username: dkl  
> Created at: 2020-12-14 14:17:42 UTC  
> Url: https://github.com/boostorg/process/issues/40#issuecomment-744469935  

The `std::terminate()` should be fixed by https://github.com/boostorg/process/pull/189.  
  
But the unit test probably needs further adjustment, because it tries to create N_cpu_cores * 100 pipes, which will fail for example on systems with 16 cores but 1024 maximum open file descriptors (see ulimit -n soft limit).
