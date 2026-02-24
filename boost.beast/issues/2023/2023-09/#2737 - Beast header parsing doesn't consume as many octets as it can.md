# #2737 - Beast header parsing doesn't consume as many octets as it can [Open]

> Username: cmazakas  
> Created at: 2023-09-08 14:43:36 UTC  
> Updated at: 2023-09-08 14:43:36 UTC  
> Url: https://github.com/boostorg/beast/issues/2737  

https://godbolt.org/z/cPd7h7n8G  
  
In the above compiler explorer sample, we see that theoretically Beast can parse up to 30 octets but instead only parses 16 and then requests that the user update the buffer.  
  
This works fine in most cases for Asio applications as buffers are entirely owned by the caller but does not work so well when using io_uring's multishot recv() method where buffers are owned by the ring so _not_ parsing as many octets as possible gums up the application.  
  
Beast should parse as many octets as possible, fully consuming the buffer so long as the provided octets are valid.  
  
Inlined example for posterity:  
```cpp  
#include <boost/beast/http/parser.hpp>  
#include <boost/beast/http/empty_body.hpp>  
#include <boost/beast/core/flat_buffer.hpp>  
#include <boost/asio/buffer.hpp>  
  
#include <iostream>  
#include <cstring>  
  
namespace http = boost::beast::http;  
namespace asio = boost::asio;  
  
int main() {  
  char const* msg="GET / HTTP/1.1\r\nConnection: cl";  
  
  http::request_parser<http::empty_body> p;  
  boost::system::error_code ec;  
  auto n = p.put(asio::buffer(msg, std::strlen(msg)), ec);  
  if (n != std::strlen(msg)) {  
    std::cout << "only parsed: " << n << " octets.\n";  
    std::cout << "we should've parsed: " << std::strlen(msg) << " octets\n";  
    return 1;  
  }  
}  
```
