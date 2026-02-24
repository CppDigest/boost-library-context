# #332 - missing inline in experimental/task_region.hpp [Open]

> Username: marcusb  
> Created at: 2020-12-08 16:51:55 UTC  
> Updated at: 2020-12-08 16:52:17 UTC  
> Url: https://github.com/boostorg/thread/issues/332  

The function `handle_task_region_exceptions` in `experimental/task_region.hpp` is missing an `inline` modifier, so it will cause a "multiple definition" error if the file is included in more than one source file.  
  
To reproduce, add a second translation unit `extra.cpp` to the `examples` directory with the single line:  
```  
#include <boost/thread/experimental/task_region.hpp>  
```  
and include it in the Jamfile:  
```  
exe fib_task_region : fib_task_region.cpp extra.cpp ;  
```  
  
This generates the following error on a Linux system with gcc 10:  
```  
> bjam fib_task_region  
warning: No toolsets are configured.  
warning: Configuring default toolset "gcc".  
warning: If the default is wrong, your build may not work correctly.  
warning: Use the "toolset=xxxxx" option to override our guess.  
warning: For more configuration options, please consult  
warning: http://boost.org/boost-build2/doc/html/bbv2/advanced/configuration.html  
Performing configuration checks  
  
    - default address-model    : 64-bit (cached)  
    - default architecture     : x86 (cached)  
    - symlinks supported       : yes (cached)  
    - lockfree boost::atomic_flag : yes (cached)  
...patience...  
...patience...  
...found 3484 targets...  
...updating 2 targets...  
gcc.compile.c++ ../../../bin.v2/libs/thread/example/gcc-10/debug/threadapi-pthread/threading-multi/visibility-hidden/extra.o  
gcc.link ../../../bin.v2/libs/thread/example/gcc-10/debug/threadapi-pthread/threading-multi/visibility-hidden/fib_task_region  
/usr/bin/ld: ../../../bin.v2/libs/thread/example/gcc-10/debug/threadapi-pthread/threading-multi/visibility-hidden/extra.o: in function `std::less<boost::system::error_category const*>::operator()(boost::system::error_category const*, boost::system::error_category const*) const':  
/home/mbetter/src/boost/libs/thread/example/../../../boost/exception/detail/exception_ptr.hpp:335: multiple definition of `boost::experimental::parallel::v2::detail::handle_task_region_exceptions(boost::experimental::parallel::v1::exception_list&)'; ../../../bin.v2/libs/thread/example/gcc-10/debug/threadapi-pthread/threading-multi/visibility-hidden/fib_task_region.o:/home/mbetter/src/boost/libs/thread/example/../../../boost/thread/experimental/parallel/v2/task_region.hpp:55: first defined here  
collect2: error: ld returned 1 exit status  
  
    "g++"  -Wl,-rpath -Wl,"/home/mbetter/src/boost/bin.v2/libs/chrono/build/gcc-10/debug/threading-multi/visibility-hidden" -Wl,-rpath -Wl,"/home/mbetter/src/boost/bin.v2/libs/thread/build/gcc-10/debug/threadapi-pthread/threading-multi/visibility-hidden" -Wl,-rpath-link -Wl,"/home/mbetter/src/boost/bin.v2/libs/chrono/build/gcc-10/debug/threading-multi/visibility-hidden" -Wl,-rpath-link -Wl,"/home/mbetter/src/boost/bin.v2/libs/thread/build/gcc-10/debug/threadapi-pthread/threading-multi/visibility-hidden" -o "../../../bin.v2/libs/thread/example/gcc-10/debug/threadapi-pthread/threading-multi/visibility-hidden/fib_task_region" -Wl,--start-group "../../../bin.v2/libs/thread/example/gcc-10/debug/threadapi-pthread/threading-multi/visibility-hidden/fib_task_region.o" "../../../bin.v2/libs/thread/example/gcc-10/debug/threadapi-pthread/threading-multi/visibility-hidden/extra.o" "../../../bin.v2/libs/thread/build/gcc-10/debug/threadapi-pthread/threading-multi/visibility-hidden/libboost_thread.so.1.75.0" "../../../bin.v2/libs/chrono/build/gcc-10/debug/threading-multi/visibility-hidden/libboost_chrono.so.1.75.0"  -Wl,-Bstatic  -Wl,-Bdynamic -lrt -Wl,--end-group -fPIC -m64 -pthread -g -fvisibility=hidden -fvisibility-inlines-hidden   
  
...failed gcc.link ../../../bin.v2/libs/thread/example/gcc-10/debug/threadapi-pthread/threading-multi/visibility-hidden/fib_task_region...  
...failed updating 1 target...  
...updated 1 target...  
```

---

## Comment 1

> Username: marcusb  
> Created at: 2020-12-08 16:52:16 UTC  
> Url: https://github.com/boostorg/thread/issues/332#issuecomment-740757307  

A fix for this is here: https://github.com/boostorg/thread/pull/331
