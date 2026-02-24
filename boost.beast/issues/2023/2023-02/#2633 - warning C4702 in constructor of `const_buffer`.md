# #2633 - warning C4702 in constructor of  `const_buffer` [Closed]

> Username: tnixeu  
> Created at: 2023-02-03 14:09:17 UTC  
> Updated at: 2024-01-30 07:08:00 UTC  
> Closed at: 2024-01-30 07:08:00 UTC  
> Url: https://github.com/boostorg/beast/issues/2633  

I am compiling my code on Windows with all warnings turned on and warnings treated as errors (`/W4` and `/WX`) and since boost 1.81.0 I get an error that some code in `<boost\asio\buffer.hpp>(247)` is unreachable. As can be seen below, the line in question is part of a constructor of `const_buffer`. This happens only in a release build.  
  
```c++  
  /// Construct a non-modifiable buffer from a modifiable one.  
  const_buffer(const mutable_buffer& b, bool =false) BOOST_ASIO_NOEXCEPT  
    : data_(b.data()),   // This is line 247  
      size_(b.size())  
#if defined(BOOST_ASIO_ENABLE_BUFFER_DEBUGGING)  
      , debug_check_(b.get_debug_check())  
#endif // BOOST_ASIO_ENABLE_BUFFER_DEBUGGING  
  {  
  }  
  ```  
  
My suspicion is, that a `const_buffer` is created in an area of `boost::beast` which is determined to be unreachable in a release build. I tried looking for this, but I was not able to find it. When removing the content of the functions in `<boost\beast\http\impl\serializer.hpp>` the warning disappears, so it might be somewhere in those functions.  
  
So far I was not able to reproduce this with boost asio alone.  
  
I am not sure if that could also be considered a bug in MSVC.  
  
### Version of Beast  
  
The boost beast version is 345 as part of 1.81.0  
  
### Steps necessary to reproduce the problem  
  
I tried finding the problem by stripping down the async tcp server example. This is the minimal example I can provide, which still causes the warning.  
  
```c++  
//  
// Copyright (c) 2016-2019 Vinnie Falco (vinnie dot falco at gmail dot com)  
//  
// Distributed under the Boost Software License, Version 1.0. (See accompanying  
// file LICENSE_1_0.txt or copy at http://www.boost.org/LICENSE_1_0.txt)  
//  
// Official repository: https://github.com/boostorg/beast  
//  
  
//------------------------------------------------------------------------------  
//  
// Example: HTTP client, asynchronous  
//  
//------------------------------------------------------------------------------  
  
#include <boost/asio/strand.hpp>  
#include <boost/beast/core.hpp>  
#include <boost/beast/http.hpp>  
#include <boost/beast/version.hpp>  
  
namespace beast = boost::beast;   // from <boost/beast.hpp>  
namespace http = beast::http;     // from <boost/beast/http.hpp>  
namespace net = boost::asio;      // from <boost/asio.hpp>  
using tcp = boost::asio::ip::tcp; // from <boost/asio/ip/tcp.hpp>  
  
// Performs an HTTP GET and prints the response  
class session : public std::enable_shared_from_this<session> {  
    beast::tcp_stream stream_;  
    http::request<http::empty_body> req_;  
  
  public:  
    // Objects are constructed with a strand to  
    // ensure that handlers do not execute concurrently.  
    explicit session(net::io_context& ioc) : stream_(net::make_strand(ioc)) {  
    }  
    void buffer_test() {  
        http::async_write(stream_, req_, beast::bind_front_handler(&session::on_write, shared_from_this()));  
    }  
  
    void on_write(beast::error_code, std::size_t) {  
    }  
};  
  
//------------------------------------------------------------------------------  
  
int main(int, char**) {  
    // The io_context is required for all I/O  
    net::io_context ioc;  
  
    // Launch the asynchronous operation  
    std::make_shared<session>(ioc)->buffer_test();  
    return EXIT_SUCCESS;  
}  
```  
  
  
  
### All relevant compiler information  
  
Compile the async tcp server example with Visual Studio 2022 with `/W4` and `/WX` enabled in release mode.  
  
CMake reports the compiler as MSVC 19.34.31937.0 the Visual Studio version is 17.4.4.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2023-02-03 14:19:32 UTC  
> Url: https://github.com/boostorg/beast/issues/2633#issuecomment-1415935166  

this could be caused by `buffer_cat` again...

---

## Comment 2

> Username: ashtum  
> Created at: 2024-01-30 07:08:00 UTC  
> Url: https://github.com/boostorg/beast/issues/2633#issuecomment-1916202927  

Fixed in https://github.com/boostorg/beast/pull/2803.
