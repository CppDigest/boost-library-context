# #201 - Segfault with empty co_main when compiling with BOOST_COBALT_NO_PMR [Closed]

> Username: nyibbang  
> Created at: 2024-09-19 14:26:55 UTC  
> Updated at: 2024-12-13 13:28:31 UTC  
> Closed at: 2024-12-13 13:28:30 UTC  
> Url: https://github.com/boostorg/cobalt/issues/201  

Hello,  
  
I'm having a segmentation fault with AddressSanitizer when building an empty co_main program while disabling usage of PMR (so I set BOOST_COBALT_NO_PMR).  
  
- OS: Ubuntu 22.04  
- Compiler: g++-13 (Ubuntu 13.1.0-8ubuntu1~22.04) 13.1.0  
- Boost version: 1.86.0  
  
Assuming I have my boost install in `/home/nyibbang/boost_install`, my compile command is:  
  
```  
g++-13 \  
    -DBOOST_COBALT_NO_PMR \  
    -g -fsanitize=address -fno-omit-frame-pointer -std=gnu++20 \  
    -isystem /home/nyibbang/boost_install/include \  
    -L/home/nyibbang/boost_install/lib \  
    ../main.cpp \  
    /home/nyibbang/boost_install/lib/libboost_cobalt.a \  
    /home/nyibbang/boost_install/lib/libboost_container.a \  
    -lrt  -lpthread \  
    -o test_cobalt  
```  
  
The `main.cpp` source file is:  
  
```c++  
#include <boost/cobalt.hpp>  
  
boost::cobalt::main co_main(int, char**) {  
    co_return 0;  
}  
```  
  
When I run the executable, I instantly get a sanitizer error:  
  
<details><summary>Output</summary>  
<p>  
  
    =================================================================  
    ==2824500==ERROR: AddressSanitizer: SEGV on unknown address (pc 0x592a8cbe1b61 bp 0x7ffc6f4ab080 sp 0x7ffc6f4ab070 T0)  
    ==2824500==The signal is caused by a READ memory access.  
    ==2824500==Hint: this fault was caused by a dereference of a high value address (see register values below).  Disassemble the provided pc to learn which register was used.  
        #0 0x592a8cbe1b61 in boost::asio::execution::detail::any_executor_base::~any_executor_base() (/home/nyibbang/test_cobalt/build/test_cobalt+0xfb61) (BuildId: 584ff56445bf020733d8703454eeb1aac63e6ab4)  
        #1 0x592a8cbe3be3 in boost::asio::execution::any_executor<boost::asio::execution::context_as_t<boost::asio::execution_context&>, boost::asio::execution::detail::blocking::never_t<0>, boost::asio::execution::prefer_only<boost::asio::execution::detail::blocking::possibly_t<0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::outstanding_work::tracked_t<0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::outstanding_work::untracked_t<0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::relationship::fork_t<0> >, boost::asio::execution::prefer_only<boost::asio::execution::detail::relationship::continuation_t<0> > >::~any_executor() (/home/nyibbang/test_cobalt/build/test_cobalt+0x11be3) (BuildId: 584ff56445bf020733d8703454eeb1aac63e6ab4)  
        #2 0x592a8cbe3c03 in boost::asio::any_io_executor::~any_io_executor() (/home/nyibbang/test_cobalt/build/test_cobalt+0x11c03) (BuildId: 584ff56445bf020733d8703454eeb1aac63e6ab4)  
        #3 0x592a8cbec87c in boost::asio::executor_work_guard<boost::asio::any_io_executor, void, void>::~executor_work_guard() (/home/nyibbang/test_cobalt/build/test_cobalt+0x1a87c) (BuildId: 584ff56445bf020733d8703454eeb1aac63e6ab4)  
        #4 0x592a8cbeafb2 in std::_Optional_payload_base<boost::asio::executor_work_guard<boost::asio::any_io_executor, void, void> >::_M_destroy() (/home/nyibbang/test_cobalt/build/test_cobalt+0x18fb2) (BuildId: 584ff56445bf020733d8703454eeb1aac63e6ab4)  
        #5 0x592a8cbe9098 in std::_Optional_payload_base<boost::asio::executor_work_guard<boost::asio::any_io_executor, void, void> >::_M_reset() (/home/nyibbang/test_cobalt/build/test_cobalt+0x17098) (BuildId: 584ff56445bf020733d8703454eeb1aac63e6ab4)  
        #6 0x592a8cbfe871 in std::_Optional_base_impl<boost::asio::executor_work_guard<boost::asio::any_io_executor, void, void>, std::_Optional_base<boost::asio::executor_work_guard<boost::asio::any_io_executor, void, void>, false, false> >::_M_reset() /usr/include/c++/13/optional:468  
        #7 0x592a8cbfd711 in std::enable_if<is_constructible_v<boost::asio::executor_work_guard<boost::asio::any_io_executor, void, void>, boost::asio::io_context::basic_executor_type<std::allocator<void>, 0ul> >, boost::asio::executor_work_guard<boost::asio::any_io_executor, void, void>&>::type std::optional<boost::asio::executor_work_guard<boost::asio::any_io_executor, void, void> >::emplace<boost::asio::io_context::basic_executor_type<std::allocator<void>, 0ul> >(boost::asio::io_context::basic_executor_type<std::allocator<void>, 0ul>&&) /usr/include/c++/13/optional:917  
        #8 0x592a8cbf1227 in boost::cobalt::detail::main_promise::run_main(boost::cobalt::main) libs/cobalt/src/main.cpp:32  
        #9 0x592a8cbe5ecb in main (/home/nyibbang/test_cobalt/build/test_cobalt+0x13ecb) (BuildId: 584ff56445bf020733d8703454eeb1aac63e6ab4)  
        #10 0x781f5e629d8f in __libc_start_call_main ../sysdeps/nptl/libc_start_call_main.h:58  
        #11 0x781f5e629e3f in __libc_start_main_impl ../csu/libc-start.c:392  
        #12 0x592a8cbdce24 in _start (/home/nyibbang/test_cobalt/build/test_cobalt+0xae24) (BuildId: 584ff56445bf020733d8703454eeb1aac63e6ab4)  
  
    AddressSanitizer can not provide additional info.  
    SUMMARY: AddressSanitizer: SEGV (/home/nyibbang/test_cobalt/build/test_cobalt+0xfb61) (BuildId: 584ff56445bf020733d8703454eeb1aac63e6ab4) in boost::asio::execution::detail::any_executor_base::~any_executor_base()  
    ==2824500==ABORTING  
  
</p>  
</details>

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2024-12-13 03:20:18 UTC  
> Url: https://github.com/boostorg/cobalt/issues/201#issuecomment-2540448171  

Is the boost_cobalt.a built with the same options as the exe?

---

## Comment 2

> Username: nyibbang  
> Created at: 2024-12-13 13:28:30 UTC  
> Url: https://github.com/boostorg/cobalt/issues/201#issuecomment-2541466737  

Mmhh, that's a very good point. I built it with Conan so no, the flag was not set. I should have thought of that, my bad.
