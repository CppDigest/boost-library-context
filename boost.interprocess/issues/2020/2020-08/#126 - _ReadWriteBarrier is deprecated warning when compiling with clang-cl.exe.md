# #126 - _ReadWriteBarrier is deprecated warning when compiling with clang-cl.exe [Closed]

> Username: k15tfu  
> Created at: 2020-08-09 13:33:09 UTC  
> Updated at: 2021-02-12 18:21:14 UTC  
> Closed at: 2021-02-12 18:21:14 UTC  
> Url: https://github.com/boostorg/interprocess/issues/126  

```  
1>In file included from boost/interprocess/mapped_region.hpp:50:  
1>In file included from boost/interprocess/sync/windows/sync_utils.hpp:25:  
1>In file included from boost/interprocess/sync/spin/mutex.hpp:26:  
1>boost/interprocess/detail/atomic.hpp(96,5): error : '_ReadWriteBarrier' is deprecated: use other intrinsics or C++11 atomics instead [-Werror,-Wdeprecated-declarations]  
1>boost/interprocess/detail/atomic.hpp(67,50): message : expanded from macro 'BOOST_INTERPROCESS_READ_WRITE_BARRIER'  
1>C:\Program Files (x86)\Microsoft Visual Studio\2019\Professional\VC\Tools\Llvm\x64\lib\clang\10.0.0\include\intrin.h(176,16): message : '_ReadWriteBarrier' has been explicitly marked deprecated here  
```

---

## Comment 1

> Username: igaztanaga  
> Created at: 2021-02-12 18:20:15 UTC  
> Url: https://github.com/boostorg/interprocess/issues/126#issuecomment-778360725  

It looks like Visual 2019 just undeprecates the function om <atomic> because there is no other alternative right now for pre-C+11 enviroments. Using an exchange-like functions would write to memory, which would trigger an access violation error for read-only shared memories. Interprocess will use the same approach Visual 2019 uses.
