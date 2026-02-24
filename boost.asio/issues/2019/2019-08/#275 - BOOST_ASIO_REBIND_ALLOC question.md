# #275 - BOOST_ASIO_REBIND_ALLOC question [Closed]

> Username: ssfang  
> Created at: 2019-08-31 14:52:18 UTC  
> Updated at: 2020-12-30 01:08:34 UTC  
> Closed at: 2020-12-30 01:08:33 UTC  
> Url: https://github.com/boostorg/asio/issues/275  

Hi,  
  
I‘m trying boost aiso examples on my andoird mobile phone.  
  
Unfortunately!  
  
When I compiled the [time_t_timer](https://www.boost.org/doc/libs/1_69_0/doc/html/boost_asio/example/cpp11/timers/time_t_timer.cpp) example, using c4doird app by `g++ -std=c++17` on android, the g++ complained:  
  
no member `allocate` and `deallocate`  
  
at the line [190](https://github.com/boostorg/asio/blob/develop/include/boost/asio/detail/handler_alloc_helpers.hpp#L190) in [handler_alloc_helpers.hpp](https://github.com/boostorg/asio/blob/develop/include/boost/asio/detail/handler_alloc_helpers.hpp), and I found it occurred when compiling the parameter of `async_wait`:  
```c++  
    timer.async_wait(  
        [](const boost::system::error_code& /*error*/)  
        {  
          std::cout << "timeout\n";  
        });  
```  
  
After I   
  
1. updated the line [59](https://github.com/boostorg/asio/blob/77d3089f2ee5939e558154a80d4dfba261d16971/include/boost/asio/detail/memory.hpp#L59) in [memory.hpp](https://www.boost.org/doc/libs/1_69_0/boost/asio/detail/memory.hpp) to:  
	```c++  
	typename std::allocator_traits<alloc>::template rebind_alloc<t>::other  
	```  
or,  
  
2. added `#define BOOST_ASIO_DISABLE_CXX11_ALLOCATORS` before `#include <boost/asio.hpp>`,  
  
The problem disappeared!  
  
Is there a problem here?  
  
https://github.com/boostorg/asio/blob/master/include/boost/asio/detail/memory.hpp

---

## Comment 1

> Username: djarek  
> Created at: 2019-09-04 16:33:46 UTC  
> Url: https://github.com/boostorg/asio/issues/275#issuecomment-527982095  

At first glance, it seems that there is a bug and your first solution should be applied.

---

## Comment 2

> Username: ghost  
> Created at: 2020-12-30 01:08:32 UTC  
> Url: https://github.com/boostorg/asio/issues/275#issuecomment-752292790  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#714](https://github.com/chriskohlhoff/asio/issues/714).
