# #230 cast-function-type MinGW warnings when looking for CancelIoEx [Closed]

> Username: mabrarov  
> Created at: 2019-04-30 08:14:54 UTC  
> Updated at: 2019-07-10 16:04:21 UTC  
> Closed at: 2019-07-10 16:04:13 UTC  
> Url: https://github.com/boostorg/asio/pull/230  

Fixed MinGW warnings about incompatible function cast when looking for entry point of CancelIoEx WinAPI function  
  
Example of warning:  
  
```  
include/boost-1_70/boost/asio/detail/impl/win_iocp_socket_service_base.ipp: In member function 'boost::system::error_code boost::asio::detail::win_iocp_socket_service_base::cancel(boost::asio::detail::win_iocp_socket_service_base::base_implementation_type&, boost::system::error_code&)':  
include/boost-1_70/boost/asio/detail/impl/win_iocp_socket_service_base.ipp:253:52: warning: cast between incompatible function types from 'FARPROC' {aka 'long long int (*)()'} to 'cancel_io_ex_t' {aka 'int (*)(void*, _OVERLAPPED*)'} [-Wcast-function-type]  
     cancel_io_ex_t cancel_io_ex = (cancel_io_ex_t) cancel_io_ex_ptr;  
                                                    ^~~~~~~~~~~~~~~~  
In file included from include/boost-1_70/boost/asio/detail/win_iocp_socket_service_base.hpp:597,  
                 from include/boost-1_70/boost/asio/detail/win_iocp_socket_accept_op.hpp:30,  
                 from include/boost-1_70/boost/asio/detail/win_iocp_socket_service.hpp:41,  
                 from include/boost-1_70/boost/asio/basic_socket.hpp:34,  
                 from include/boost-1_70/boost/asio/basic_datagram_socket.hpp:20,  
                 from include/boost-1_70/boost/asio.hpp:24,  
```  
  
Reproduced with Boost 1.70.0 and MinGW 8.1.0 from [mingw-w64](https://sourceforge.net/projects/mingw-w64/) project. Used compiler optinos: `-Wall -Wextra -pedantic -Wunused`  
  
This pull request does the same as [pull request #389](https://github.com/chriskohlhoff/asio/pull/389) in [chriskohlhoff/asio](https://github.com/chriskohlhoff/asio) repository.

---

## Comment 1

> Username: wyattoday  
> Created_at: 2019-05-30 12:19:17 UTC  
> Url: https://github.com/boostorg/asio/pull/230#issuecomment-497310429  

This also happens with MSVC 14.2 (Visual Studio 2019). This pull request fixes it. Here are the exact warnings:  
  
```  
boost\asio\detail\impl\win_iocp_socket_service_base.ipp(253,67): warning C4191:  'type cast': unsafe conversion from 'FARPROC' to 'cancel_io_ex_t'  
```

---

## Comment 2

> Username: wyattoday  
> Created_at: 2019-07-10 15:40:49 UTC  
> Url: https://github.com/boostorg/asio/pull/230#issuecomment-510115641  

Looks like this fixed in this commit: https://github.com/boostorg/asio/commit/539145bb7455b043098152658a9dc251949da23b (without accepting the PR for some reason)

---

## Comment 3

> Username: mabrarov  
> Created_at: 2019-07-10 16:04:13 UTC  
> Url: https://github.com/boostorg/asio/pull/230#issuecomment-510125068  

I'm closing this PR as not needed anymore taking into account [comment](https://github.com/boostorg/asio/pull/230#issuecomment-510115641) of @wyattoday

---
