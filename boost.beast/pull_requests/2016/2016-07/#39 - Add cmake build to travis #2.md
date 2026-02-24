# #39 Add cmake build to travis #2 [Closed]

> Username: wilsonianb  
> Created at: 2016-07-06 18:22:05 UTC  
> Updated at: 2017-07-25 17:44:16 UTC  
> Closed at: 2016-07-22 12:42:38 UTC  
> Url: https://github.com/boostorg/beast/pull/39  

based on https://github.com/vinniefalco/Beast/pull/29

---

## Comment 1

> Username: coveralls  
> Created_at: 2016-07-06 18:44:11 UTC  
> Url: https://github.com/boostorg/beast/pull/39#issuecomment-230866657  

[![Coverage Status](https://coveralls.io/builds/6893940/badge)](https://coveralls.io/builds/6893940)  
  
Coverage remained the same at 98.116% when pulling **6cdfe394a48899a282df0e6d1a35f0a7b1ad8397 on wilsonianb:cmake** into **f8a1ec03480ce255dfe7f834524749e454925f42 on vinniefalco:master**.

---

## Comment 2

> Username: codecov-io  
> Created_at: 2016-07-06 18:45:18 UTC  
> Url: https://github.com/boostorg/beast/pull/39#issuecomment-230866999  

## [Current coverage](https://codecov.io/gh/vinniefalco/Beast/pull/39?src=pr) is **98.11%**  
  
> Merging [#39](https://codecov.io/gh/vinniefalco/Beast/pull/39?src=pr) into [master](https://codecov.io/gh/vinniefalco/Beast/branch/master?src=pr) will increase coverage by **0.02%**  
  
``` diff  
@@             master        #39   diff @@  
==========================================  
  Files            72         71      -1     
  Lines          5083       4035   -1048     
  Methods           0          0             
  Messages          0          0             
  Branches          0          0             
==========================================  
- Hits           4986       3959   -1027     
+ Misses           97         76     -21     
  Partials          0          0             
```  
  
> Powered by [Codecov](https://codecov.io?src=pr). Last updated by [f8a1ec0...6cdfe39](https://codecov.io/gh/vinniefalco/Beast/compare/f8a1ec03480ce255dfe7f834524749e454925f42...6cdfe394a48899a282df0e6d1a35f0a7b1ad8397)

---

## Comment 3

> Username: seelabs  
> Created_at: 2016-07-21 19:36:34 UTC  
> Url: https://github.com/boostorg/beast/pull/39#discussion_r71772289  

We might want to remove that message. It looks like debug code that was accidentally left in.

---

## Comment 4

> Username: seelabs  
> Created_at: 2016-07-21 19:38:16 UTC  
> Url: https://github.com/boostorg/beast/pull/39#discussion_r71772553  

Not for today, but we might want to clean this up to use `add_compile_options` instead.

---

## Comment 5

> Username: seelabs  
> Created_at: 2016-07-21 19:50:57 UTC  
> Url: https://github.com/boostorg/beast/pull/39#issuecomment-234363887  

:+1:

---

## Comment 6

> Username: jnohlgard  
> Created_at: 2016-07-22 08:12:36 UTC  
> Updated_at: 2016-07-22 08:12:37 UTC  
> Url: https://github.com/boostorg/beast/pull/39#discussion_r71842123  

This line (and the subsequent similar elseifs) fails if cmake is run without `-DVARIANT=anything`.  
  
```  
~/src/beast/build % cmake ..  
-- The C compiler identification is GNU 5.4.0  
-- The CXX compiler identification is GNU 5.4.0  
-- Check for working C compiler: /usr/bin/cc  
-- Check for working C compiler: /usr/bin/cc -- works  
-- Detecting C compiler ABI info  
-- Detecting C compiler ABI info - done  
-- Detecting C compile features  
-- Detecting C compile features - done  
-- Check for working CXX compiler: /usr/bin/c++  
-- Check for working CXX compiler: /usr/bin/c++ -- works  
-- Detecting CXX compiler ABI info  
-- Detecting CXX compiler ABI info - done  
-- Detecting CXX compile features  
-- Detecting CXX compile features - done  
-- Looking for pthread.h  
-- Looking for pthread.h - found  
-- Looking for pthread_create  
-- Looking for pthread_create - not found  
-- Looking for pthread_create in pthreads  
-- Looking for pthread_create in pthreads - not found  
-- Looking for pthread_create in pthread  
-- Looking for pthread_create in pthread - found  
-- Found Threads: TRUE    
-- Boost version: 1.61.0  
-- Found the following Boost libraries:  
--   coroutine  
--   context  
--   thread  
--   filesystem  
--   program_options  
--   system  
--   chrono  
--   date_time  
--   atomic  
-- Check if compiler accepts -pthread  
-- Check if compiler accepts -pthread - yes  
CMake Error at CMakeLists.txt:26 (if):  
  if given arguments:  
  
    "STREQUAL" "coverage"  
  
  Unknown arguments specified  
  
  
-- Configuring incomplete, errors occurred!  
```

---

## Comment 7

> Username: vinniefalco  
> Created_at: 2016-07-22 15:31:36 UTC  
> Url: https://github.com/boostorg/beast/pull/39#discussion_r71898057  

Not sure how this popped up after the code was merged but its being looked into, thanks.

---
