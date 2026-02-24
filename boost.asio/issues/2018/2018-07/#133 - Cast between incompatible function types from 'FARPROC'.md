# #133 - Cast between incompatible function types from 'FARPROC' [Closed]

> Username: kivadiu  
> Created at: 2018-07-29 17:39:25 UTC  
> Updated at: 2020-12-30 00:52:31 UTC  
> Closed at: 2020-12-30 00:52:30 UTC  
> Url: https://github.com/boostorg/asio/issues/133  

When I compile this simple program:  
```  
#include <boost/asio/ip/tcp.hpp>  
int main() {  
  return 0;  
}  
```  
using mingw-w64 + g++ 8.2.0 + boost 1.68.0 beta1:  
`x86_64-w64-mingw32-g++-8.2.0 -o acceptor.o -c -O2  -Wall -Wextra -std=c++14 -I/softs/win64-mingw-8.2.0/release/boost/include Progs/acceptor.cpp`  
  
I get the following warning:  
```  
In file included from /softs/win64-mingw-8.2.0/release/boost/include/boost/asio/detail/win_iocp_socket_service_base.hpp:588,  
                 from /softs/win64-mingw-8.2.0/release/boost/include/boost/asio/detail/win_iocp_socket_accept_op.hpp:30,  
                 from /softs/win64-mingw-8.2.0/release/boost/include/boost/asio/detail/win_iocp_socket_service.hpp:41,  
                 from /softs/win64-mingw-8.2.0/release/boost/include/boost/asio/basic_socket.hpp:37,  
                 from /softs/win64-mingw-8.2.0/release/boost/include/boost/asio/basic_socket_acceptor.hpp:20,  
                 from /softs/win64-mingw-8.2.0/release/boost/include/boost/asio/ip/tcp.hpp:19,  
                 from Progs/acceptor.cpp:1:  
/softs/win64-mingw-8.2.0/release/boost/include/boost/asio/detail/impl/win_iocp_socket_service_base.ipp: In member function 'boost::system::error_code boost::asio::detail::win_iocp_socket_service_base::cancel(boost::asio::detail::win_iocp_socket_service_base::base_implementation_type&, boost::system::error_code&)':  
/softs/win64-mingw-8.2.0/release/boost/include/boost/asio/detail/impl/win_iocp_socket_service_base.ipp:253:51: warning: cast between incompatible function types from 'FARPROC' {aka 'long long int (*)()'} to 'cancel_io_ex_t' {aka 'int (*)(void*, _OVERLAPPED*)'} [-Wcast-function-type]  
     cancel_io_ex_t cancel_io_ex = (cancel_io_ex_t)cancel_io_ex_ptr;  
                                                   ^~~~~~~~~~~~~~~~  
```

---

## Comment 1

> Username: Nomadluap  
> Created at: 2018-08-17 18:06:17 UTC  
> Url: https://github.com/boostorg/asio/issues/133#issuecomment-413945432  

I can confirm this issue.   
  
GCC (MinGW-W64 64-bit)  
    > g++ --version  
    g++.exe (x86_64-posix-seh-rev0, Built by MinGW-W64 project) 8.1.0  
    Copyright (C) 2018 Free Software Foundation, Inc.  
    This is free software; see the source for copying conditions.  There is NO  
    warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  
  
Boost 1.68.0

---

## Comment 2

> Username: MarcelRaad  
> Created at: 2018-08-17 20:06:16 UTC  
> Url: https://github.com/boostorg/asio/issues/133#issuecomment-413974846  

This new GCC 8 warning doesn't make much sense on Windows, you can hardly avoid it when working with the Windows SDK. Maybe reporting this to GCC or MinGW developers would make more sense than here.

---

## Comment 3

> Username: kivadiu  
> Created at: 2018-08-19 17:43:44 UTC  
> Url: https://github.com/boostorg/asio/issues/133#issuecomment-414143483  

But it could make sense for other use cases.  
For example this program triggers the warning:  
```  
int g(int i) { return i; }  
  
int main() {  
  using func_t = int (*)();  
  auto ptr = (func_t)&g;  
  return 0;  
}  
```  
```  
toto.cpp: In function ‘int main()’:  
toto.cpp:5:23: warning: cast between incompatible function types from ‘int (*)(int)’ to ‘func_t’ {aka ‘int (*)()’} [-Wcast-function-type]  
   auto ptr = (func_t)&g;  
                       ^  
```  
  
How could the compiler make the difference between being in the Windows SDK or not?

---

## Comment 4

> Username: MarcelRaad  
> Created at: 2018-08-19 21:33:28 UTC  
> Url: https://github.com/boostorg/asio/issues/133#issuecomment-414157508  

It probably cannot, but `FARPROC` could be whitelisted in MinGW as is `void (*) (void)`.

---

## Comment 5

> Username: ghost  
> Created at: 2020-12-30 00:52:29 UTC  
> Url: https://github.com/boostorg/asio/issues/133#issuecomment-752289760  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#631](https://github.com/chriskohlhoff/asio/issues/631).
