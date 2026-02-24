# #254 - valgrind identified UMR in an example [Open]

> Username: jeking3  
> Created at: 2018-11-21 12:19:37 UTC  
> Updated at: 2019-05-09 19:27:31 UTC  
> Url: https://github.com/boostorg/thread/issues/254  

https://travis-ci.org/jeking3/thread/jobs/457654740#L6689  
```  
testing.capture-output ../../bin.v2/libs/thread/test/ex_make_future_lib.test/clang-linux-6.0/debug/cxxstd-03-iso/threadapi-pthread/threading-multi/visibility-hidden/ex_make_future_lib.run  
====== BEGIN OUTPUT ======  
==33300== Memcheck, a memory error detector  
==33300== Copyright (C) 2002-2013, and GNU GPL'd, by Julian Seward et al.  
==33300== Using Valgrind-3.10.1 and LibVEX; rerun with -h for copyright info  
==33300== Command: ../../bin.v2/libs/thread/test/ex_make_future_lib.test/clang-linux-6.0/debug/cxxstd-03-iso/threadapi-pthread/threading-multi/visibility-hidden/ex_make_future_lib  
==33300==   
../../libs/thread/test/../example/make_future.cpp 92  
../../libs/thread/test/../example/make_future.cpp 98  
../../libs/thread/test/../example/make_future.cpp 103  
0  
../../libs/thread/test/../example/make_future.cpp 108  
==33300== Invalid read of size 4  
==33300==    at 0x405B19: main (make_future.cpp:110)  
==33300==  Address 0xffeffed4c is on thread 1's stack  
==33300==  244 bytes below stack pointer  
==33300==   
```  
  
I haven't tried it with the latest valgrind however.  Current version is 3.14.0 and this was detected in 3.10.0.

---

## Comment 1

> Username: viboes  
> Created at: 2018-11-21 21:01:01 UTC  
> Url: https://github.com/boostorg/thread/issues/254#issuecomment-440806980  

What can be interpreted this report?

---

## Comment 2

> Username: jeking3  
> Created at: 2018-11-21 22:40:55 UTC  
> Url: https://github.com/boostorg/thread/issues/254#issuecomment-440834292  

I am not sure just yet.  I updated the CI environment to download and build Valgrind 3.14.0 to see if this was a failing of Valgrind 3.10.0 or not.  I'll update this when I know more.

---

## Comment 3

> Username: viboes  
> Created at: 2018-11-25 07:36:20 UTC  
> Url: https://github.com/boostorg/thread/issues/254#issuecomment-441421541  

On which platform is this failing?

---

## Comment 4

> Username: jeking3  
> Created at: 2018-11-25 12:57:43 UTC  
> Url: https://github.com/boostorg/thread/issues/254#issuecomment-441438628  

I see this on linux with clang 6.0 and valgrind (versions 3.10.0 and 3.14.0).  
```  
testing.capture-output ../../bin.v2/libs/thread/test/ex_make_future.test/clang-linux-6.0/debug/cxxstd-11-iso/threadapi-pthread/threading-multi/visibility-hidden/ex_make_future.run  
====== BEGIN OUTPUT ======  
==46392== Memcheck, a memory error detector  
==46392== Copyright (C) 2002-2017, and GNU GPL'd, by Julian Seward et al.  
==46392== Using Valgrind-3.14.0 and LibVEX; rerun with -h for copyright info  
==46392== Command: ../../bin.v2/libs/thread/test/ex_make_future.test/clang-linux-6.0/debug/cxxstd-11-iso/threadapi-pthread/threading-multi/visibility-hidden/ex_make_future  
==46392==   
../../libs/thread/test/../example/make_future.cpp 92  
../../libs/thread/test/../example/make_future.cpp 98  
../../libs/thread/test/../example/make_future.cpp 103  
0  
../../libs/thread/test/../example/make_future.cpp 108  
==46392== Invalid read of size 4  
==46392==    at 0x408286: main (make_future.cpp:110)  
==46392==  Address 0x1ffeffecec is on thread 1's stack  
==46392==  132 bytes below stack pointer  
==46392==   
0  
../../libs/thread/test/../example/make_future.cpp 121  
0  
../../libs/thread/test/../example/make_future.cpp 127  
0  
../../libs/thread/test/../example/make_future.cpp 133  
5  
../../libs/thread/test/../example/make_future.cpp 138  
0  
```

---

## Comment 5

> Username: viboes  
> Created at: 2018-11-25 13:53:43 UTC  
> Url: https://github.com/boostorg/thread/issues/254#issuecomment-441442072  

Oh, I see it now "clang-linux-6.0/". I was expecting to see it on the job title.
