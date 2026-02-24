# #287 - Boost::fiber's library doesn't seem to get built in 1.78 [Closed]

> Username: 20k  
> Created at: 2021-12-16 19:55:21 UTC  
> Updated at: 2022-03-18 01:17:05 UTC  
> Closed at: 2021-12-17 07:29:43 UTC  
> Url: https://github.com/boostorg/fiber/issues/287  

Hi there. Compiling boost under wsl2 (ubuntu) with GCC-10 doesn't seem to produce any libboost_fiber libraries on 1.78. 1.76 seems to work fine  
  
The command line I used for both was: ./b2 -j32 variant=release link=shared threading=multi runtime-link=shared  
  
Variations of --with-fiber, or cxxflags="-std=c++14" (as well as 11 and 17) did not cause boost to build the library. It reports as a success, but there's simply no shared object at the end of it. In the end I reverted to 1.76, which correctly builds  
  
Thanks!

---

## Comment 1

> Username: olk  
> Created at: 2021-12-17 07:25:13 UTC  
> Updated at: 2021-12-17 07:29:04 UTC  
> Url: https://github.com/boostorg/fiber/issues/287#issuecomment-996495511  

Can't reproduce your problem:  
- gcc-11  
- arch linux  
- ./b2 --with-fiber -j 25 variant=release link=shared threading=multi runtime-link=shared  
- ls bin.v2/libs/fiber/build/gcc-11/release/threading-multi/visibility-hidden/  
algo/  barrier.o  condition_variable.o  context.o  fiber.o  future.o  libboost_fiber-variant-shared.cmake  libboost_fiber.so.1.78.0*  mutex.o  properties.o  recursive_mutex.o  recursive_timed_mutex.o  scheduler.o  timed_mutex.o  waker.o

---

## Comment 2

> Username: 20k  
> Created at: 2021-12-18 02:22:01 UTC  
> Url: https://github.com/boostorg/fiber/issues/287#issuecomment-997127004  

Interesting, I didn't think to check under bin.v2/libs/fiber/build/gcc-10/release/threading-multi/visibility-hidden/  
  
In 1.78 libboost_fiber.so.1.78.0 is built there successfully, but there's nothing under ./stage/lib and boost fiber fails to install correctly. In 1.76 the .so is produced both in the bin.v2 segment, as well as in ./stage/lib along with the corresponding symlinks. It looks possibly like some kind of failure of the build system

---

## Comment 3

> Username: 20k  
> Created at: 2021-12-18 02:23:46 UTC  
> Url: https://github.com/boostorg/fiber/issues/287#issuecomment-997127237  

This looks like its probably the same issue as https://github.com/boostorg/boost/issues/594 and #286

---

## Comment 4

> Username: olk  
> Created at: 2021-12-18 08:25:29 UTC  
> Url: https://github.com/boostorg/fiber/issues/287#issuecomment-997169043  

I think this issue is related to the cmake stuff.

---

## Comment 5

> Username: brad0  
> Created at: 2022-03-18 01:17:05 UTC  
> Url: https://github.com/boostorg/fiber/issues/287#issuecomment-1071926816  

This is probably related to the  known issues listed item..  
  
https://www.boost.org/patches/1_78_0/0001-b2-fix-install.patch
