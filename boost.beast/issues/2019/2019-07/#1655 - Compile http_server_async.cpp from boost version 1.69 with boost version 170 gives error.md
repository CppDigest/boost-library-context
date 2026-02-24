# #1655 - Compile http_server_async.cpp from boost version 1.69 with boost version 170 gives error [Closed]

> Username: mr-j0nes  
> Created at: 2019-07-15 10:55:28 UTC  
> Updated at: 2019-07-15 15:28:15 UTC  
> Closed at: 2019-07-15 15:28:15 UTC  
> Url: https://github.com/boostorg/beast/issues/1655  

Hello there,  
  
thanks for the awesomeness of this library.  
  
I am searching to have compatibility between the two last versions of boost but if I compile `http_server_async.cpp` from boost version 1.69 with boost version 1.70 I get following error:  
  
> http_server_async.cpp: In constructor ‘session::session(boost::asio::ip::tcp::socket, const std::shared_ptr<const std::__cxx11::basic_string<char> >&)’:  
> http_server_async.cpp:278:24: error: no matching function for call to ‘boost::asio::strand<boost::asio::io_context::executor_type>::strand(boost::asio::basic_socket<boost::asio::ip::tcp, boost::asio::executor>::executor_type)’  
>          , lambda_(*this)  
>                         ^  
> In file included from http_server_async.cpp:21:0:  
> /home/javier/develop/sandbox/boost/boost_1_70_0/boost/asio/strand.hpp:111:3: note: candidate: template<class OtherExecutor> boost::asio::strand<Executor>::strand(boost::asio::strand<OtherExecutor>&&)  
>    strand(strand<OtherExecutor>&& other) BOOST_ASIO_NOEXCEPT  
>    ^~~~~~  
> /home/javier/develop/sandbox/boost/boost_1_70_0/boost/asio/strand.hpp:111:3: note:   template argument deduction/substitution failed:  
> http_server_async.cpp:278:24: note:   ‘boost::asio::basic_socket<boost::asio::ip::tcp, boost::asio::executor>::executor_type {aka boost::asio::executor}’ is not derived from ‘boost::asio::strand<Executor>’  
>          , lambda_(*this)  
>                         ^  
> In file included from http_server_async.cpp:21:0:  
> /home/javier/develop/sandbox/boost/boost_1_70_0/boost/asio/strand.hpp:99:3: note: candidate: boost::asio::strand<Executor>::strand(boost::asio::strand<Executor>&&) [with Executor = boost::asio::io_context::executor_type]  
>    strand(strand&& other) BOOST_ASIO_NOEXCEPT  
>    ^~~~~~  
> /home/javier/develop/sandbox/boost/boost_1_70_0/boost/asio/strand.hpp:99:3: note:   no known conversion for argument 1 from ‘boost::asio::basic_socket<boost::asio::ip::tcp, boost::asio::executor>::executor_type {aka boost::asio::executor}’ to ‘boost::asio::strand<boost::asio::io_context::executor_type>&&’  
> /home/javier/develop/sandbox/boost/boost_1_70_0/boost/asio/strand.hpp:68:3: note: candidate: template<class OtherExecutor> boost::asio::strand<Executor>::strand(const boost::asio::strand<OtherExecutor>&)  
>    strand(  
>    ^~~~~~  
> /home/javier/develop/sandbox/boost/boost_1_70_0/boost/asio/strand.hpp:68:3: note:   template argument deduction/substitution failed:  
> http_server_async.cpp:278:24: note:   ‘boost::asio::basic_socket<boost::asio::ip::tcp, boost::asio::executor>::executor_type {aka boost::asio::executor}’ is not derived from ‘const boost::asio::strand<Executor>’  
>          , lambda_(*this)  
>                         ^  
> In file included from http_server_async.cpp:21:0:  
> /home/javier/develop/sandbox/boost/boost_1_70_0/boost/asio/strand.hpp:56:3: note: candidate: boost::asio::strand<Executor>::strand(const boost::asio::strand<Executor>&) [with Executor = boost::asio::io_context::executor_type]  
>    strand(const strand& other) BOOST_ASIO_NOEXCEPT  
>    ^~~~~~  
> /home/javier/develop/sandbox/boost/boost_1_70_0/boost/asio/strand.hpp:56:3: note:   no known conversion for argument 1 from ‘boost::asio::basic_socket<boost::asio::ip::tcp, boost::asio::executor>::executor_type {aka boost::asio::executor}’ to ‘const boost::asio::strand<boost::asio::io_context::executor_type>&’  
> /home/javier/develop/sandbox/boost/boost_1_70_0/boost/asio/strand.hpp:48:12: note: candidate: boost::asio::strand<Executor>::strand(const Executor&) [with Executor = boost::asio::io_context::executor_type]  
>    explicit strand(const Executor& e)  
>             ^~~~~~  
> /home/javier/develop/sandbox/boost/boost_1_70_0/boost/asio/strand.hpp:48:12: note:   no known conversion for argument 1 from ‘boost::asio::basic_socket<boost::asio::ip::tcp, boost::asio::executor>::executor_type {aka boost::asio::executor}’ to ‘const boost::asio::io_context::executor_type&’  
> /home/javier/develop/sandbox/boost/boost_1_70_0/boost/asio/strand.hpp:40:3: note: candidate: boost::asio::strand<Executor>::strand() [with Executor = boost::asio::io_context::executor_type]  
>    strand()  
>    ^~~~~~  
> /home/javier/develop/sandbox/boost/boost_1_70_0/boost/asio/strand.hpp:40:3: note:   candidate expects 0 arguments, 1 provided  
   
  
I am not sure I am understanding the problem, according to the `asio` release notes  `socket_.get_executor()` should still work here. Any help is really appreciated..  
  
My compiler's version:  
  
> g++ (Ubuntu 7.4.0-1ubuntu1~18.04.1) 7.4.0  
> Copyright (C) 2017 Free Software Foundation, Inc.  
> This is free software; see the source for copying conditions.  There is NO  
> warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-07-15 14:35:31 UTC  
> Url: https://github.com/boostorg/beast/issues/1655#issuecomment-511428744  

You can't mix and match examples and library headers. If you are using Boost 1.70 then you have to use the examples from 1.70. Also note that there were significant changes for 1.70 in how executors are associated with I/O objects in Asio, and Beast adopted these changes as well.

---

## Comment 2

> Username: mr-j0nes  
> Created at: 2019-07-15 15:28:08 UTC  
> Url: https://github.com/boostorg/beast/issues/1655#issuecomment-511449893  

I kind of thought the example was adapted to make use of the latest implemented features and that the library was still compatible with older version..  
  
I have an HTTP server based on `http_server_async.cpp` (boost version 1.69), I guess I have to refactor it as that example for boost 1.70.  
  
Ok excellent will do that and keep backwards compatibility with `#if BOOST_VERSION ....`  
  
Thank you very much for the help..
