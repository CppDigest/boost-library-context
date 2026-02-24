# #39 - atomic/detail/fence_arch_ops_gcc_arm.hpp:70:13: error: expected string literal in 'asm' [Closed]

> Username: Kojoley  
> Created at: 2020-07-01 20:03:13 UTC  
> Updated at: 2020-07-01 20:17:14 UTC  
> Closed at: 2020-07-01 20:16:19 UTC  
> Url: https://github.com/boostorg/atomic/issues/39  

https://www.boost.org/development/tests/develop/developer/output/teeks99-05-dg8-2a-64onarmv7l-boost-bin-v2-libs-atomic-test-atomic_api-test-gcc-8~c++2a-debug-address-model-64-threadapi-pthread-threading-multi-visibility-hidden.html  
  
```  
    "g++-8"   -fvisibility-inlines-hidden -std=c++2a -fPIC -pthread -O0 -fno-inline -Wall -g -fvisibility=hidden  -DBOOST_ALL_NO_LIB=1 -DBOOST_ATOMIC_DYN_LINK=1 -DBOOST_BEAST_ALLOW_DEPRECATED -DBOOST_BEAST_SEPARATE_COMPILATION -DBOOST_CHRONO_DYN_LINK=1 -DBOOST_THREAD_BUILD_DLL=1 -DBOOST_THREAD_POSIX -DBOOST_THREAD_USE_DLL=1  -I".."  -c -o "/mnt/fs1/teeks99-05/run/results/boost/bin.v2/libs/atomic/test/atomic_api.test/gcc-8~c++2a/debug/address-model-64/threadapi-pthread/threading-multi/visibility-hidden/atomic_api.o" "../libs/atomic/test/atomic_api.cpp"  
  
In file included from ../boost/atomic/detail/fence_arch_operations.hpp:21,  
                 from ../boost/atomic/detail/ops_gcc_arm_common.hpp:22,  
                 from ../boost/atomic/detail/core_arch_ops_gcc_arm.hpp:26,  
                 from ../boost/atomic/detail/core_arch_operations.hpp:24,  
                 from ../boost/atomic/detail/core_operations.hpp:19,  
                 from ../boost/atomic/detail/atomic_impl.hpp:27,  
                 from ../boost/atomic/atomic.hpp:26,  
                 from ../boost/atomic.hpp:15,  
                 from ../libs/atomic/test/atomic_api.cpp:8:  
../boost/atomic/detail/fence_arch_ops_gcc_arm.hpp: In static member function ???static void boost::atomics::detail::fence_arch_operations_gcc_arm::hardware_full_fence()???:  
../boost/atomic/detail/fence_arch_ops_gcc_arm.hpp:70:13: error: expected string-literal before ???BOOST_ATOMIC_DETAIL_ARM_ASM_START???  
             BOOST_ATOMIC_DETAIL_ARM_ASM_START(%0)  
             ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
```

---

## Comment 1

> Username: Lastique  
> Created at: 2020-07-01 20:17:14 UTC  
> Url: https://github.com/boostorg/atomic/issues/39#issuecomment-652625401  

Thanks.
