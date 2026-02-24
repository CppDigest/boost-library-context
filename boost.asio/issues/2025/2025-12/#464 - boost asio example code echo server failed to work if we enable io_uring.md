# #464 - [need help] boost asio example code echo server failed to work if we enable io_uring [Open]

> Username: gaowayne  
> Created at: 2025-12-31 15:37:46 UTC  
> Updated at: 2026-01-03 02:52:14 UTC  
> Url: https://github.com/boostorg/asio/issues/464  

hello expert  
  
I run boost asio echo example with io_uring. but I saw below error code, same code works fine with epoll mode.  
  
```  
Exception: io_uring_get_sqe: No buffer space available [system:105]  
```

---

## Comment 1

> Username: gaowayne  
> Created at: 2026-01-03 02:52:14 UTC  
> Url: https://github.com/boostorg/asio/issues/464#issuecomment-3706634100  

boost asio expert,   
here is my example echo server from asio example  
```  
//  
// echo_server.cpp - Based on Boost 1.90 example  
// echo_server_with_as_single_default.cpp  
//  
  
#include <boost/asio/experimental/as_single.hpp>  
#include <boost/asio/co_spawn.hpp>  
#include <boost/asio/detached.hpp>  
#include <boost/asio/io_context.hpp>  
#include <boost/asio/ip/tcp.hpp>  
#include <boost/asio/signal_set.hpp>  
#include <boost/asio/write.hpp>  
#include <boost/asio/executor_work_guard.hpp>  
#include <cstdio>  
#include <iostream>  
#include <unistd.h>  // for getpid()  
  
using boost::asio::experimental::as_single_t;  
using boost::asio::ip::tcp;  
using boost::asio::awaitable;  
using boost::asio::co_spawn;  
using boost::asio::detached;  
using boost::asio::use_awaitable_t;  
using default_token = as_single_t<use_awaitable_t<>>;  
using tcp_acceptor = default_token::as_default_on_t<tcp::acceptor>;  
using tcp_socket = default_token::as_default_on_t<tcp::socket>;  
namespace this_coro = boost::asio::this_coro;  
  
awaitable<void> echo(tcp_socket socket)  
{  
  char data[1024];  
  for (;;)  
  {  
    auto [e1, nread] = co_await socket.async_read_some(boost::asio::buffer(data));  
    if (nread == 0) break;  
    auto [e2, nwritten] = co_await async_write(socket, boost::asio::buffer(data, nread));  
    if (nwritten != nread) break;  
  }  
}  
  
awaitable<void> listener()  
{  
  auto executor = co_await this_coro::executor;  
  tcp_acceptor acceptor(executor, {tcp::v4(), 55555});  
  std::cout << "Echo server listening on port 55555\n";  
  for (;;)  
  {  
    if (auto [e, socket] = co_await acceptor.async_accept(); socket.is_open())  
    {  
      std::cout << "New connection accepted\n";  
      co_spawn(executor, echo(std::move(socket)), detached);  
    }  
  }  
}  
  
int main()  
{  
  try  
  {  
    std::cout << "Starting echo server...\n";  
    std::cout << "Press Ctrl+C to stop\n";  
  
    boost::asio::io_context io_context(1);  
  
    // TEMPORARY WORKAROUND: Disable signal_set to test if io_uring works without it  
    // The issue is that signal_set creation triggers register_internal_io_object  
    // which calls get_sqe() and fails with ENOBUFS  
    // Use 'kill' command to stop the server: kill <pid>  
    std::cout << "WARNING: Signal handling disabled for io_uring testing\n";  
    std::cout << "Use 'kill <pid>' to stop the server\n";  
    std::cout << "Server PID: " << getpid() << "\n";  
  
    // Create a work guard to keep io_context alive  
    auto work_guard = boost::asio::make_work_guard(io_context);  
  
    // Start listener  
    co_spawn(io_context, listener(), detached);  
  
    // NOTE: signal_set creation is disabled - this is a known issue with io_uring  
    // boost::asio::signal_set signals(io_context, SIGINT, SIGTERM);  
    // signals.async_wait([&](auto, auto){ io_context.stop(); });  
  
    std::cout << "Starting event loop...\n";  
    io_context.run();  
  
    std::cout << "Server stopped\n";  
  }  
  catch (std::exception& e)  
  {  
    std::cerr << "Exception: " << e.what() << "\n";  
    return 1;  
  }  
  
  return 0;  
}  
```  
here is my test client code  
```  
#!/bin/bash  
# Simple test client for echo server  
  
HOST=${1:-localhost}  
PORT=${2:-55555}  
  
echo "Connecting to $HOST:$PORT..."  
echo "Type messages and press Enter. Type 'quit' to exit."  
echo ""  
  
while true; do  
    read -p "> " message  
    if [ "$message" = "quit" ]; then  
        break  
    fi  
    echo "$message" | nc $HOST $PORT  
done  
  
echo "Disconnected"  
```  
  
after I run test client, it is just hanging never hello never get response back.  
  
==client side==  
root@salab-smcamd02:/mnt/nvme4n1/wayne/s3proxy/S3LightBoost/rpcs3_io_uring/test_boost_example# sh test_client.sh  
Connecting to localhost:55555...  
Type messages and press Enter. Type 'quit' to exit.  
  
> hello  
  
  
===server side===  
(s3proxy) root@salab-smcamd02:/mnt/nvme4n1/wayne/s3proxy/S3LightBoost/rpcs3_io_uring/test_boost_example/build# ./echo_server_io_uring  
Starting echo server...  
Press Ctrl+C to stop  
WARNING: Signal handling disabled for io_uring testing  
Use 'kill <pid>' to stop the server  
Server PID: 1091794  
Starting event loop...  
Echo server listening on port 55555  
  
===my cmakelist.txt===  
```  
cmake_minimum_required(VERSION 3.16)  
  
project(BoostEchoServerTest  
  VERSION 1.0.0  
  LANGUAGES CXX)  
  
set(CMAKE_CXX_STANDARD 20)  
set(CMAKE_CXX_STANDARD_REQUIRED ON)  
set(CMAKE_CXX_EXTENSIONS OFF)  
  
# Find Boost  
if(DEFINED ENV{Boost_ROOT})  
    set(Boost_ROOT $ENV{Boost_ROOT})  
    message(STATUS "Using Boost_ROOT from environment: ${Boost_ROOT}")  
elseif(DEFINED ENV{BOOST_ROOT})  
    set(Boost_ROOT $ENV{BOOST_ROOT})  
    message(STATUS "Using BOOST_ROOT from environment: ${Boost_ROOT}")  
endif()  
  
set(Boost_USE_STATIC_LIBS OFF)  
set(Boost_USE_DEBUG_RUNTIME OFF)  
set(Boost_USE_MULTITHREADED ON)  
set(Boost_USE_STATIC_RUNTIME OFF)  
  
find_package(Boost 1.90 QUIET)  
if(NOT Boost_FOUND)  
    find_package(Boost 1.82 QUIET)  
endif()  
  
if(NOT Boost_FOUND)  
    message(FATAL_ERROR "Boost 1.82+ not found. Please install Boost or set Boost_ROOT")  
endif()  
  
message(STATUS "Boost version: ${Boost_VERSION}")  
message(STATUS "Boost include dirs: ${Boost_INCLUDE_DIRS}")  
  
# Version 1: Default (epoll) - no special flags  
add_executable(echo_server_epoll echo_server.cpp)  
target_include_directories(echo_server_epoll PRIVATE ${Boost_INCLUDE_DIRS})  
target_compile_definitions(echo_server_epoll PRIVATE BOOST_ASIO_NO_DEPRECATED)  
if(TARGET Boost::system)  
    target_link_libraries(echo_server_epoll PRIVATE Boost::system)  
endif()  
  
# Version 2: io_uring version  
add_executable(echo_server_io_uring echo_server.cpp)  
target_include_directories(echo_server_io_uring PRIVATE ${Boost_INCLUDE_DIRS})  
target_compile_definitions(echo_server_io_uring PRIVATE   
    BOOST_ASIO_NO_DEPRECATED  
    BOOST_ASIO_HAS_IO_URING=1  
    BOOST_ASIO_DISABLE_EPOLL=1  
    BOOST_ASIO_HAS_IO_URING_AS_DEFAULT=1  
)  
if(TARGET Boost::system)  
    target_link_libraries(echo_server_io_uring PRIVATE Boost::system)  
endif()  
  
# Find liburing for io_uring version  
find_library(URING_LIBRARY NAMES uring)  
if(URING_LIBRARY)  
    message(STATUS "Found liburing: ${URING_LIBRARY}")  
    target_link_libraries(echo_server_io_uring PRIVATE ${URING_LIBRARY})  
else()  
    message(WARNING "liburing not found - io_uring version may not work")  
endif()  
  
```
