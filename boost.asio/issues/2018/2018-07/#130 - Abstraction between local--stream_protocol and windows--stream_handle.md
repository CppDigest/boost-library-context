# #130 - Abstraction between local::stream_protocol and windows::stream_handle [Closed]

> Username: fabiencastan  
> Created at: 2018-07-15 09:24:33 UTC  
> Updated at: 2020-12-30 00:52:22 UTC  
> Closed at: 2020-12-30 00:52:21 UTC  
> Url: https://github.com/boostorg/asio/issues/130  

In Boost ASIO, we have access to `boost::asio::local::stream_protocol` on unix and `boost::asio::windows::stream_handle` on windows. This is great to have access to low level feature of each one. But is it possible to have a multi-platform abstraction layer with the lowest-common-denominator? Or is there already one that I missed?  
  
For instance:  
- libuv provides an abstraction over local domain sockets on Unix and named pipes on Windows:  
http://docs.libuv.org/en/v1.x/pipe.html  
- Qt provides this abstraction in QLocalSocket:  
"The QLocalSocket class provides a local socket. On Windows this is a named pipe and on Unix this is a local domain socket.":  
https://doc.qt.io/Qt-5/qlocalsocket.html

---

## Comment 1

> Username: mehrdadn  
> Created at: 2020-03-07 22:55:15 UTC  
> Url: https://github.com/boostorg/asio/issues/130#issuecomment-596145751  

Hi, I was wondering if there has been any progress on this? Is there any common abstraction between Win32 named pipes and UNIX-domain sockets?

---

## Comment 2

> Username: djarek  
> Created at: 2020-03-08 00:30:49 UTC  
> Updated at: 2020-03-08 00:32:51 UTC  
> Url: https://github.com/boostorg/asio/issues/130#issuecomment-596152193  

You could use [generic stream protocol sockets](https://www.boost.org/doc/libs/1_72_0/doc/html/boost_asio/reference/generic__stream_protocol/socket.html), you'll just need an ifdef in a factory function.  
  
```cpp  
template <class Executor>  
net::generic::stream_protocol::socket  
make_local_socket(Executor const& e,/*parameters*/)  
{  
#ifdef USE_WINDOWS_PIPE  
  return net::windows::stream_handle{e};  
#elif USE_UNIX_DOMAIN_SOCKET  
  return net::local::stream_protocol{e};  
#else  
  #error Unknown IPC method  
#endif  
}  
```

---

## Comment 3

> Username: mehrdadn  
> Created at: 2020-03-08 00:33:10 UTC  
> Url: https://github.com/boostorg/asio/issues/130#issuecomment-596152325  

Oh wow I didn't realize this was possible. Will look into it, thank you!

---

## Comment 4

> Username: mehrdadn  
> Created at: 2020-03-08 04:36:43 UTC  
> Url: https://github.com/boostorg/asio/issues/130#issuecomment-596165882  

Sorry to bother you again, but are these both compatible with `generic::stream_protocol::socket`? I'm getting an error when compiling this code... I'm not sure if I'm using them incorrectly or not:  
  
	#include <boost/asio.hpp>  
  
	boost::asio::generic::stream_protocol::socket  
		make_local_socket(boost::asio::io_service &io_service)  
	{  
	#ifdef BOOST_ASIO_HAS_LOCAL_SOCKETS  
		return boost::asio::local::stream_protocol(io_service);  
	#endif  
	#ifdef BOOST_ASIO_HAS_WINDOWS_STREAM_HANDLE  
		return boost::asio::windows::stream_handle(io_service);  
	#endif  
	}  
  
> error C2440: `return`: cannot convert from `boost::asio::windows::stream_handle` to `boost::asio::basic_stream_socket<boost::asio::generic::stream_protocol,boost::asio::executor>`  
> note: Constructor for class `boost::asio::basic_stream_socket<boost::asio::generic::stream_protocol,boost::asio::executor>` is declared `explicit`  
  
Using explicit constructors doesn't seem to help either:  
  
	#include <boost/asio.hpp>  
  
	boost::asio::generic::stream_protocol::socket  
		make_local_socket(boost::asio::io_service &io_service)  
	{  
	#ifdef BOOST_ASIO_HAS_LOCAL_SOCKETS  
		boost::asio::local::stream_protocol unix(io_service);  
		boost::asio::generic::stream_protocol::socket result(std::move(unix));  
	#endif  
	#ifdef BOOST_ASIO_HAS_WINDOWS_STREAM_HANDLE  
		boost::asio::windows::stream_handle windows(io_service);  
		boost::asio::generic::stream_protocol::socket result(std::move(windows));  
	#endif  
		return result;  
	}  
  
> boost/asio/impl/executor.hpp(218): error C2280: `boost::asio::windows::basic_stream_handle<boost::asio::executor>::basic_stream_handle(const boost::asio::windows::basic_stream_handle<boost::asio::executor> &)`: attempting to reference a deleted function  
> boost/asio/windows/basic_stream_handle.hpp(465): note: compiler has generated `boost::asio::windows::basic_stream_handle<boost::asio::executor>::basic_stream_handle` here  
> boost/asio/impl/executor.hpp(213): note: while compiling class template member function `bool boost::asio::executor::impl<Executor,std::allocator<void>>::equals(const boost::asio::executor::impl_base *) noexcept const`  
>        with Executor=boost::asio::windows::basic_stream_handle<boost::asio::executor>  
> boost/asio/impl/executor.hpp(333): note: see reference to class template instantiation `boost::asio::executor::impl<Executor,std::allocator<void>>` being compiled  
>        with Executor=boost::asio::windows::basic_stream_handle<boost::asio::executor>  
> temp.cpp(12): note: see reference to function template instantiation `boost::asio::executor::executor<boost::asio::windows::basic_stream_handle<boost::asio::executor>>(Executor)` being compiled  
>        with Executor=boost::asio::windows::basic_stream_handle<boost::asio::executor>  
> boost/asio/use_future.hpp(139): note: see reference to class template instantiation `boost::asio::use_future_t<std::allocator<void>>::std_allocator_void` being compiled  
> boost/asio/use_future.hpp(147): note: see reference to class template instantiation `boost::asio::use_future_t<std::allocator<void>>` being compiled  
  
Am I using these incorrectly by any chance?  
  
Thank you!

---

## Comment 5

> Username: djarek  
> Created at: 2020-03-08 14:47:08 UTC  
> Url: https://github.com/boostorg/asio/issues/130#issuecomment-596213527  

Hmm, I only use generic streams to store either local domain or tcp sockets. Looks like it's trying to use the windows stream handle as an executor, so it may be a bug in ASIO.

---

## Comment 6

> Username: ghost  
> Created at: 2020-12-30 00:52:20 UTC  
> Url: https://github.com/boostorg/asio/issues/130#issuecomment-752289731  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#629](https://github.com/chriskohlhoff/asio/issues/629).
