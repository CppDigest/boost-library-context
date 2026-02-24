# #173 - Travis failures with std::thread [Closed]

> Username: pdimov  
> Created at: 2017-10-08 19:51:34 UTC  
> Updated at: 2017-10-17 05:41:42 UTC  
> Closed at: 2017-10-09 08:53:30 UTC  
> Url: https://github.com/boostorg/thread/issues/173  

The Travis jobs using the default g++ 4.8 installation fail with  
  
```  
gcc.compile.c++ bin.v2/libs/thread/test/ex_std_thread_guard.test/gcc-gnu-4.8/debug/cxxstd-11/threadapi-pthread/threading-multi/std_thread_guard.o  
gcc.link bin.v2/libs/thread/test/ex_std_thread_guard.test/gcc-gnu-4.8/debug/cxxstd-11/threadapi-pthread/threading-multi/ex_std_thread_guard  
gcc.compile.c++ bin.v2/libs/thread/test/ex_std_thread_guard_lib.test/gcc-gnu-4.8/debug/cxxstd-11/threadapi-pthread/threading-multi/std_thread_guard.o  
gcc.link bin.v2/libs/thread/test/ex_std_thread_guard_lib.test/gcc-gnu-4.8/debug/cxxstd-11/threadapi-pthread/threading-multi/ex_std_thread_guard_lib  
testing.capture-output bin.v2/libs/thread/test/ex_std_thread_guard_lib.test/gcc-gnu-4.8/debug/cxxstd-11/threadapi-pthread/threading-multi/ex_std_thread_guard_lib.run  
====== BEGIN OUTPUT ======  
terminate called after throwing an instance of 'std::system_error'  
  what():  Enable multithreading to use std::thread: Operation not permitted  
Aborted (core dumped)  
  
EXIT STATUS: 134  
====== END OUTPUT ======  
```  
  
That's odd, I'm not sure what we need to do to "enable multithreading" there.  
  
See f.ex. https://travis-ci.org/boostorg/boost/jobs/285103799

---

## Comment 1

> Username: pdimov  
> Created at: 2017-10-08 21:15:09 UTC  
> Url: https://github.com/boostorg/thread/issues/173#issuecomment-335039096  

Could be this bug: https://bugs.launchpad.net/ubuntu/+source/gcc-defaults/+bug/1228201

---

## Comment 2

> Username: viboes  
> Created at: 2017-10-08 21:16:05 UTC  
> Url: https://github.com/boostorg/thread/issues/173#issuecomment-335039148  

I've no idea what need to be done. I've never had such issue on the regression tests of multiple platforms and compilers.  
  
I believe that it is the build system that enables multithreading with the threading=multi.  
  
I would suggest to create an issue on the build system.

---

## Comment 3

> Username: viboes  
> Created at: 2017-10-08 21:20:46 UTC  
> Url: https://github.com/boostorg/thread/issues/173#issuecomment-335039421  

Yes, maybe it is the same bug.  
  
For my side, I purpose to don't support this platform/compiler/version.  
  
If some one has the time to see how to make it working, please send a PR.

---

## Comment 4

> Username: pdimov  
> Created at: 2017-10-09 01:07:32 UTC  
> Url: https://github.com/boostorg/thread/issues/173#issuecomment-335051766  

See #174.

---

## Comment 5

> Username: pdimov  
> Created at: 2017-10-09 08:53:30 UTC  
> Url: https://github.com/boostorg/thread/issues/173#issuecomment-335099179  

Works now. :-)  
  
https://travis-ci.org/boostorg/boost/builds/285389334

---

## Comment 6

> Username: viboes  
> Created at: 2017-10-09 10:54:34 UTC  
> Url: https://github.com/boostorg/thread/issues/173#issuecomment-335124389  

Thanks
