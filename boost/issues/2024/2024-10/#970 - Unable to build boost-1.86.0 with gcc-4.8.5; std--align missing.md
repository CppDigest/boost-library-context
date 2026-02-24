# #970 - Unable to build boost-1.86.0 with gcc-4.8.5; std::align missing [Closed]

> Username: utoni  
> Created at: 2024-10-25 14:03:28 UTC  
> Updated at: 2025-09-01 13:14:22 UTC  
> Closed at: 2025-09-01 13:14:22 UTC  
> Url: https://github.com/boostorg/boost/issues/970  

I am getting the following build error:  
```  
gcc.compile.c++ bin.v2/libs/process/build/gcc-4.8/release/x86_64/cxxstd-11-iso/threading-multi/visibility-hidden/ext/env.o  
In file included from ./boost/asio/detail/handler_alloc_helpers.hpp:19:0,  
                 from ./boost/asio/detail/executor_function.hpp:19,  
                 from ./boost/asio/execution/any_executor.hpp:24,  
                 from ./boost/asio/execution.hpp:19,  
                 from ./boost/asio/any_io_executor.hpp:22,  
                 from ./boost/process/v2/detail/process_handle_signal.hpp:25,  
                 from ./boost/process/v2/process_handle.hpp:20,  
                 from ./boost/process/v2/ext/env.hpp:14,  
                 from libs/process/src/ext/env.cpp:14:  
./boost/asio/detail/memory.hpp: In function 'void* boost::asio::detail::align(std::size_t, std::size_t, void*&, std::size_t&)':  
./boost/asio/detail/memory.hpp:58:10: error: 'align' is not a member of 'std'  
   return std::align(alignment, size, ptr, space);  
          ^  
```  
`std::align` seems to not be available on `gcc-4.8.5` for whatever reason, but building `boost-1.85.0` with the same CXX works without any issues.  
Any hints?

---

## Comment 1

> Username: templateU  
> Created at: 2025-06-12 06:52:05 UTC  
> Url: https://github.com/boostorg/boost/issues/970#issuecomment-2965344288  

this is a known missing feature for gcc😂  
see: https://stackoverflow.com/questions/27064791/stdalign-not-supported-by-g4-9

---

## Comment 2

> Username: nigels-com  
> Created at: 2025-08-31 05:02:04 UTC  
> Url: https://github.com/boostorg/boost/issues/970#issuecomment-3239766086  

@utoni Is there a current Linux distribution still providing gcc-4.8.5?  
It appears that there is [a package](https://launchpad.net/~ubuntu-toolchain-r/+archive/ubuntu/test?field.series_filter=xenial) for [Ubuntu 16.04 Xenial](https://ubuntu.com/16-04), for example.

---

## Comment 3

> Username: utoni  
> Created at: 2025-09-01 13:13:43 UTC  
> Url: https://github.com/boostorg/boost/issues/970#issuecomment-3242334548  

Unfortunately, there are still companies out there, which are using CentOS 7..

---

## Comment 4

> Username: utoni  
> Created at: 2025-09-01 13:14:22 UTC  
> Url: https://github.com/boostorg/boost/issues/970#issuecomment-3242337430  

(issue was fixed by compiling gcc-11)
