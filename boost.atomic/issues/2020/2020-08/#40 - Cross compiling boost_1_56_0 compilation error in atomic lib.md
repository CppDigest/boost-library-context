# #40 - Cross compiling boost_1_56_0 compilation error in atomic lib [Closed]

> Username: Santhosh-K-M  
> Created at: 2020-08-27 10:23:36 UTC  
> Updated at: 2020-08-27 13:42:35 UTC  
> Closed at: 2020-08-27 13:42:35 UTC  
> Url: https://github.com/boostorg/atomic/issues/40  

Hi Sir,  
I am getting the following compilation error, while cross compiling boost_1_56_0 for raspberry pi.  
However this error is not there in the boost_1_57_0.  
But I donot have the luxury to upgrade to the newer version.  
Could you please let me know how to fix this error on boost_1_56_0 its self.  
  
Following are the compilation errors:  
------------------------------------------------  
    _"/home/centos/casoc-toolchain/arm-bcm2708/arm-rpi-4.9.3-linux-gnueabihf/bin/arm-linux-gnueabihf-g++"  -ftemplate-depth-128 -O3 -finline-functions -Wno-inline -Wall -fPIC -pthread -fPIC  -DBOOST_ALL_NO_LIB=1 -DBOOST_ATOMIC_DYN_LINK=1 -DBOOST_ATOMIC_SOURCE -DNDEBUG  -I"." -c -o "bin.v2/libs/atomic/build/gcc-any/release/threading-multi/lockpool.o" "libs/atomic/src/lockpool.cpp"  
  
libs/atomic/src/lockpool.cpp: In static member function 'static void boost::atomics::detail::lockpool::thread_fence()':  
libs/atomic/src/lockpool.cpp:127:5: error: 'thread_fence' is not a member of 'boost::atomics::detail'  
     atomics::detail::thread_fence(memory_order_seq_cst);  
     ^  
libs/atomic/src/lockpool.cpp: In static member function 'static void boost::atomics::detail::lockpool::signal_fence()':  
libs/atomic/src/lockpool.cpp:138:5: error: 'signal_fence' is not a member of 'boost::atomics::detail'  
     atomics::detail::signal_fence(memory_order_seq_cst);  
     ^  
...skipped <pbin.v2/libs/atomic/build/gcc-any/release/threading-multi>libboost_atomic.so.1.56.0 for lack of <pbin.v2/libs/atomic/build/gcc-any/release/threading-multi>lockpool.o...  
...skipped <pstage/lib>libboost_atomic.so.1.56.0 for lack of <pbin.v2/libs/atomic/build/gcc-any/release/threading-multi>libboost_atomic.so.1.56.0...  
...skipped <pstage/lib>libboost_atomic.so for lack of <pstage/lib>libboost_atomic.so.1.56.0...  
common.mkdir bin.v2/libs/system/build/gcc-any/release_  
  
  
Following are the 4 steps followed:  
----------------------------------------------------  
1)Download the cross compiler using: "git clone git://github.com/raspberrypi/tools.git"  
  
2)Inside boost folder run:  
./bootstrap.sh  
  
3)Add the below line to project-config.jam  
using gcc : any : /home/centos/rpi-toolchain-downloaded/tools/arm-bcm2708/arm-rpi-4.9.3-linux-gnueabihf/bin/arm-linux-gnueabihf-g++ : <compileflags>-fPIC ;  
  
4)Compiled it using:   
./bjam -a -d+2  
  
  
Thanks in advance,  
Santhosh

---

## Comment 1

> Username: Lastique  
> Created at: 2020-08-27 10:49:32 UTC  
> Updated at: 2020-08-27 10:51:15 UTC  
> Url: https://github.com/boostorg/atomic/issues/40#issuecomment-681873509  

I suspect that the problem is that you don't specify target CPU architecture, and gcc defaults to some old ARM generation that didn't support atomic instructions. Because of this Boost.Atomic does not implement lock-free atomic operations and does not include the operations backend [here](https://github.com/boostorg/atomic/blob/boost-1.56.0/src/lockpool.cpp#L22) but uses fences [here](https://github.com/boostorg/atomic/blob/boost-1.56.0/src/lockpool.cpp#L124-L140).  
  
There are many changes to the gcc_atomic backend (which should be used in your case) in 1.57, so maybe it was indirectly fixed there. Most probably, the fix was in boost/atomic/detail/caps_gcc_atomic.hpp, in how it defines `BOOST_ATOMIC_FLAG_LOCK_FREE`. The includes and use of fences in lockpool.cpp is the same though.  
  
I think the correct solution is to specify the correct `-march` argument in the compiler command line so that atomic instructions supported by your CPU are enabled.

---

## Comment 2

> Username: Lastique  
> Created at: 2020-08-27 11:30:12 UTC  
> Url: https://github.com/boostorg/atomic/issues/40#issuecomment-681890920  

Based on google results, it seems you're using the first revision of Raspberry Pi with BCM2835 CPU. That CPU should support ARMv6Z instruction set, so you can specify `-march=armv6z` in the compiler command line.

---

## Comment 3

> Username: Santhosh-K-M  
> Created at: 2020-08-27 13:20:21 UTC  
> Url: https://github.com/boostorg/atomic/issues/40#issuecomment-681944598  

HI Lastique,   
It worked, thanks for the help. I had to use -march=armv7-a in my case.
