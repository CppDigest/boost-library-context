# #1245 - Invalid UTF8 payload error for valid Unicode strings [Closed]

> Username: bsergeev  
> Created at: 2018-09-14 21:29:58 UTC  
> Updated at: 2021-05-22 06:27:00 UTC  
> Closed at: 2018-09-24 02:37:31 UTC  
> Url: https://github.com/boostorg/beast/issues/1245  

### Version of Beast  
1.68.0 in Windows 10 and 1.67.0 in Ubuntu 16  
  
### Steps necessary to reproduce the problem  
Run the code below with `#define SCENARIO 0`  
Other SCENARIO values demonstrate successful operation.  
  
### All relevant compiler information  
Tested with Visual Studio 15.8.4 in Windows 10 and with GCC 6.4.0 in Ubuntu 16.04  
  
### Code  
The following code is based on Boost Beast synchronous WebSocket client sample, https://www.boost.org/doc/libs/1_68_0/libs/beast/example/websocket/client/sync-ssl/websocket_client_sync_ssl.cpp, with two differences:  
- the string to send to an echo server is hard-coded and controlled by SCENARIO define  
- CA certificates aren't loaded and `ws.next_layer().set_verify_mode(ssl::verify_none)` is executed instead.  
  
```  
// Show unexpected websocket::error::bad_frame_payload when receiving test_string  
// from a WS echo server.  
// Different values of SCENARIO lead to:  
//   0 - complete string => ERROR  
//   1 - only Bengali characters => success  
//   2 - all, except for Bengali characters => success  
// Just combining the Unicode strings, without enclosing ASCII (in raw string  
// literals below) is not enough to break UTF8 check. Only the complete string,  
// SCENARIO == 0, causes the error.  
//  
// Sending the complete string to an echo server from a web page  
//  https://websocket.org/echo.html  
// works fine, i.e. browser doesn't consider this Unicode string invalid  
#define SCENARIO 0  
  
#include <boost/beast/core.hpp>  
#include <boost/beast/websocket.hpp>  
#include <boost/beast/websocket/ssl.hpp>  
#include <boost/asio/connect.hpp>  
#include <boost/asio/ip/tcp.hpp>  
#include <boost/asio/ssl/stream.hpp>  
#include <cstdlib>  
#include <iostream>  
#include <string>  
  
using tcp = boost::asio::ip::tcp;               // from <boost/asio/ip/tcp.hpp>  
namespace ssl = boost::asio::ssl;               // from <boost/asio/ssl.hpp>  
namespace websocket = boost::beast::websocket;  // from <boost/beast/websocket.hpp>  
  
// Sends a WebSocket message and prints the response  
int main(int, char**)  
{  
  // Characters copied from http://www.ltg.ed.ac.uk/~richard/unicode-sample.html  
  // then converted to HEX at https://r12a.github.io/app-conversion/  
  static const std::string text_japanese =  
    "JAPANESE: "  
    "\x48\x69\x72\x61\x67\x61\x6E\x61\x3A\x20\x28\x49\x72\x6F\x68\x61\x29\x0A\xE3\x81\x84\xE3\x82\x8D\xE3\x81\xAF\xE3"  
    "\x81\xAB\xE3\x81\xBB\xE3\x81\xB8\xE3\x81\xA8\xE3\x81\xA1\xE3\x82\x8A\xE3\x81\xAC\xE3\x82\x8B\xE3\x82\x92\x0A\xE3"  
    "\x82\x8F\xE3\x81\x8B\xE3\x82\x88\xE3\x81\x9F\xE3\x82\x8C\xE3\x81\x9D\xE3\x81\xA4\xE3\x81\xAD\xE3\x81\xAA\xE3\x82"  
    "\x89\xE3\x82\x80\x0A\xE3\x81\x86\xE3\x82\x90\xE3\x81\xAE\xE3\x81\x8A\xE3\x81\x8F\xE3\x82\x84\xE3\x81\xBE\xE3\x81"  
    "\x91\xE3\x81\xB5\xE3\x81\x93\xE3\x81\x88\xE3\x81\xA6\x0A\xE3\x81\x82\xE3\x81\x95\xE3\x81\x8D\xE3\x82\x86\xE3\x82"  
    "\x81\xE3\x81\xBF\xE3\x81\x97\xE3\x82\x91\xE3\x81\xB2\xE3\x82\x82\xE3\x81\x9B\xE3\x81\x99\x0A\x0A\x4B\x61\x74\x61"  
    "\x6B\x61\x6E\x61\x3A\x0A\xE3\x82\xA4\xE3\x83\xAD\xE3\x83\x8F\xE3\x83\x8B\xE3\x83\x9B\xE3\x83\x98\xE3\x83\x88\x20"  
    "\xE3\x83\x81\xE3\x83\xAA\xE3\x83\x8C\xE3\x83\xAB\xE3\x83\xB2\x20\xE3\x83\xAF\xE3\x82\xAB\xE3\x83\xA8\xE3\x82\xBF"  
    "\xE3\x83\xAC\xE3\x82\xBD\x20\xE3\x83\x84\xE3\x83\x8D\xE3\x83\x8A\xE3\x83\xA9\xE3\x83\xA0\x0A\xE3\x82\xA6\xE3\x83"  
    "\xB0\xE3\x83\x8E\xE3\x82\xAA\xE3\x82\xAF\xE3\x83\xA4\xE3\x83\x9E\x20\xE3\x82\xB1\xE3\x83\x95\xE3\x82\xB3\xE3\x82"  
    "\xA8\xE3\x83\x86\x20\xE3\x82\xA2\xE3\x82\xB5\xE3\x82\xAD\xE3\x83\xA6\xE3\x83\xA1\xE3\x83\x9F\xE3\x82\xB7\x20\xE3"  
    "\x83\xB1\xE3\x83\x92\xE3\x83\xA2\xE3\x82\xBB\xE3\x82\xB9\xE3\x83\xB3";  
  
  static const std::string text_bengali =  
    "BENGALI: "  
    "\xE0\xA6\x81\x20\xE0\xA6\x82\x20\xE0\xA6\x83\x20\xE0\xA6\x85\x20\xE0\xA6\x86\x20\xE0\xA6\x87\x20\xE0\xA6\x88\x20"  
    "\xE0\xA6\x89\x20\xE0\xA6\x8A\x20\xE0\xA6\x8B\x20\xE0\xA6\x8C\x20\xE0\xA6\x8F\x20\xE0\xA6\x90\x20\xE0\xA6\x93\x20"  
    "\xE0\xA6\x94\x20\xE0\xA6\x95\x20\xE0\xA6\x96\x20\xE0\xA6\x97\x20\xE0\xA6\x98\x20\xE0\xA6\x99\x20\xE0\xA6\x9A\x20"  
    "\xE0\xA6\x9B\x20\xE0\xA6\x9C\x20\xE0\xA6\x9D\x20\xE0\xA6\x9E\x20\xE0\xA6\x9F\x20\xE0\xA6\xA0\x20\xE0\xA6\xA1\x20"  
    "\xE0\xA6\xA2\x20\xE0\xA6\xA3\x20\xE0\xA6\xA4\x20\xE0\xA6\xA5\x20\xE0\xA6\xA6\x20\xE0\xA6\xA7\x20\xE0\xA6\xA8\x20"  
    "\xE0\xA6\xAA\x20\xE0\xA6\xAB\x20\xE0\xA6\xAC\x20\xE0\xA6\xAD\x20\xE0\xA6\xAE\x20\xE0\xA6\xAF\x20\xE0\xA6\xB0\x20"  
    "\xE0\xA6\xB2\x20\xE0\xA6\xB6\x20\xE0\xA6\xB7\x20\xE0\xA6\xB8\x20\xE0\xA6\xB9\x20\xE0\xA6\xBC\x20\xE0\xA6\xBE\x20"  
    "\xE0\xA6\xBF\x20\xE0\xA7\x80\x20\xE0\xA7\x81\x20\xE0\xA7\x82\x20\xE0\xA7\x83\x20\xE0\xA7\x84\x20\xE0\xA7\x87\x20"  
    "\xE0\xA7\x88\x20\xE0\xA7\x8B\x20\xE0\xA7\x8C\x20\xE0\xA7\x8D\x20\xE0\xA7\x97\x20\xE0\xA7\x9C\x20\xE0\xA7\x9D\x20"  
    "\xE0\xA7\x9F\x20\xE0\xA7\xA0\x20\xE0\xA7\xA1\x20\xE0\xA7\xA2\x20\xE0\xA7\xA3\x20\xE0\xA7\xA6\x20\xE0\xA7\xA7\x20"  
    "\xE0\xA7\xA8\x20\xE0\xA7\xA9\x20\xE0\xA7\xAA\x20\xE0\xA7\xAB\x20\xE0\xA7\xAC\x20\xE0\xA7\xAD\x20\xE0\xA7\xAE\x20"  
    "\xE0\xA7\xAF\x20\xE0\xA7\xB0\x20\xE0\xA7\xB1\x20\xE0\xA7\xB2\x20\xE0\xA7\xB3\x20\xE0\xA7\xB4\x20\xE0\xA7\xB5\x20"  
    "\xE0\xA7\xB6\x20\xE0\xA7\xB7\x20\xE0\xA7\xB8\x20\xE0\xA7\xB9\x20\xE0\xA7\xBA";  
  
  
  static const std::string text_enclosed_alphanumerics =  
    "ENCLOSED ALPHANUM: "  
    "\xE2\x91\xA0\x20\xE2\x91\xA1\x20\xE2\x91\xA2\x20\xE2\x91\xA3\x20\xE2\x91\xA4\x20\xE2\x91\xA5\x20\xE2\x91\xA6\x20"  
    "\xE2\x91\xA7\x20\xE2\x91\xA8\x20\xE2\x91\xA9\x20\xE2\x91\xAA\x20\xE2\x91\xAB\x20\xE2\x91\xAC\x20\xE2\x91\xAD\x20"  
    "\xE2\x91\xAE\x20\xE2\x91\xAF\x20\xE2\x91\xB0\x20\xE2\x91\xB1\x20\xE2\x91\xB2\x20\xE2\x91\xB3\x20\xE2\x91\xB4\x20"  
    "\xE2\x91\xB5\x20\xE2\x91\xB6\x20\xE2\x91\xB7\x20\xE2\x91\xB8\x20\xE2\x91\xB9\x20\xE2\x91\xBA\x20\xE2\x91\xBB\x20"  
    "\xE2\x91\xBC\x20\xE2\x91\xBD\x20\xE2\x91\xBE\x20\xE2\x91\xBF\x20\xE2\x92\x80\x20\xE2\x92\x81\x20\xE2\x92\x82\x20"  
    "\xE2\x92\x83\x20\xE2\x92\x84\x20\xE2\x92\x85\x20\xE2\x92\x86\x20\xE2\x92\x87\x20\xE2\x92\x88\x20\xE2\x92\x89\x20"  
    "\xE2\x92\x8A\x20\xE2\x92\x8B\x20\xE2\x92\x8C\x20\xE2\x92\x8D\x20\xE2\x92\x8E\x20\xE2\x92\x8F\x20\xE2\x92\x90\x20"  
    "\xE2\x92\x91\x20\xE2\x92\x92\x20\xE2\x92\x93\x20\xE2\x92\x94\x20\xE2\x92\x95\x20\xE2\x92\x96\x20\xE2\x92\x97\x20"  
    "\xE2\x92\x98\x20\xE2\x92\x99\x20\xE2\x92\x9A\x20\xE2\x92\x9B\x20\xE2\x92\x9C\x20\xE2\x92\x9D\x20\xE2\x92\x9E\x20"  
    "\xE2\x92\x9F\x20\xE2\x92\xA0\x20\xE2\x92\xA1\x20\xE2\x92\xA2\x20\xE2\x92\xA3\x20\xE2\x92\xA4\x20\xE2\x92\xA5\x20"  
    "\xE2\x92\xA6\x20\xE2\x92\xA7\x20\xE2\x92\xA8\x20\xE2\x92\xA9\x20\xE2\x92\xAA\x20\xE2\x92\xAB\x20\xE2\x92\xAC\x20"  
    "\xE2\x92\xAD\x20\xE2\x92\xAE\x20\xE2\x92\xAF\x20\xE2\x92\xB0\x20\xE2\x92\xB1\x20\xE2\x92\xB2\x20\xE2\x92\xB3\x20"  
    "\xE2\x92\xB4\x20\xE2\x92\xB5\x20\xE2\x92\xB6\x20\xE2\x92\xB7\x20\xE2\x92\xB8\x20\xE2\x92\xB9\x20\xE2\x92\xBA\x20"  
    "\xE2\x92\xBB\x20\xE2\x92\xBC\x20\xE2\x92\xBD\x20\xE2\x92\xBE\x20\xE2\x92\xBF\x20\xE2\x93\x80\x20\xE2\x93\x81\x20"  
    "\xE2\x93\x82\x20\xE2\x93\x83\x20\xE2\x93\x84\x20\xE2\x93\x85\x20\xE2\x93\x86\x20\xE2\x93\x87\x20\xE2\x93\x88\x20"  
    "\xE2\x93\x89\x20\xE2\x93\x8A\x20\xE2\x93\x8B\x20\xE2\x93\x8C\x20\xE2\x93\x8D\x20\xE2\x93\x8E\x20\xE2\x93\x8F\x20"  
    "\xE2\x93\x90\x20\xE2\x93\x91\x20\xE2\x93\x92\x20\xE2\x93\x93\x20\xE2\x93\x94\x20\xE2\x93\x95\x20\xE2\x93\x96\x20"  
    "\xE2\x93\x97\x20\xE2\x93\x98\x20\xE2\x93\x99\x20\xE2\x93\x9A\x20\xE2\x93\x9B\x20\xE2\x93\x9C\x20\xE2\x93\x9D\x20"  
    "\xE2\x93\x9E\x20\xE2\x93\x9F";  
  
  static const std::string text_cjk_unified_ideographs =  
    "CJK UNIFIED IDEOGRAPHS: "  
    "\xE4\xB8\x80\x20\xE4\xB8\x81\x20\xE4\xB8\x82\x20\xE4\xB8\x83\x20\xE4\xB8\x84\x20\xE4\xB8\x85\x20\xE4\xB8\x86\x20"  
    "\xE4\xB8\x87\x20\xE4\xB8\x88\x20\xE4\xB8\x89\x20\xE4\xB8\x8A\x20\xE4\xB8\x8B\x20\xE4\xB8\x8C\x20\xE4\xB8\x8D\x20"  
    "\xE4\xB8\x8E\x20\xE4\xB8\x8F\x20\xE4\xB8\x90\x20\xE4\xB8\x91\x20\xE4\xB8\x92\x20\xE4\xB8\x93\x20\xE4\xB8\x94\x20"  
    "\xE4\xB8\x95\x20\xE4\xB8\x96\x20\xE4\xB8\x97\x20\xE4\xB8\x98\x20\xE4\xB8\x99\x20\xE4\xB8\x9A\x20\xE4\xB8\x9B\x20"  
    "\xE4\xB8\x9C\x20\xE4\xB8\x9D\x20\xE4\xB8\x9E\x20\xE4\xB8\x9F\x20\xE4\xB8\xA0\x20\xE4\xB8\xA1\x20\xE4\xB8\xA2\x20"  
    "\xE4\xB8\xA3\x20\xE4\xB8\xA4\x20\xE4\xB8\xA5\x20\xE4\xB8\xA6\x20\xE4\xB8\xA7\x20\xE4\xB8\xA8\x20\xE4\xB8\xA9\x20"  
    "\xE4\xB8\xAA\x20\xE4\xB8\xAB\x20\xE4\xB8\xAC\x20\xE4\xB8\xAD\x20\xE4\xB8\xAE\x20\xE4\xB8\xAF\x20\xE4\xB8\xB0\x20"  
    "\xE4\xB8\xB1\x20\xE4\xB8\xB2\x20\xE4\xB8\xB3\x20\xE4\xB8\xB4\x20\xE4\xB8\xB5\x20\xE4\xB8\xB6\x20\xE4\xB8\xB7\x20"  
    "\xE4\xB8\xB8\x20\xE4\xB8\xB9\x20\xE4\xB8\xBA\x20\xE4\xB8\xBB\x20\xE4\xB8\xBC\x20\xE4\xB8\xBD\x20\xE4\xB8\xBE\x20"  
    "\xE4\xB8\xBF\x20\xE4\xB9\x80\x20\xE4\xB9\x81\x20\xE4\xB9\x82\x20\xE4\xB9\x83\x20\xE4\xB9\x84\x20\xE4\xB9\x85\x20"  
    "\xE4\xB9\x86\x20\xE4\xB9\x87\x20\xE4\xB9\x88\x20\xE4\xB9\x89\x20\xE4\xB9\x8A\x20\xE4\xB9\x8B\x20\xE4\xB9\x8C\x20"  
    "\xE4\xB9\x8D\x20\xE4\xB9\x8E\x20\xE4\xB9\x8F\x20\xE4\xB9\x90\x20\xE4\xB9\x91\x20\xE4\xB9\x92\x20\xE4\xB9\x93\x20"  
    "\xE4\xB9\x94\x20\xE4\xB9\x95\x20\xE4\xB9\x96\x20\xE4\xB9\x97\x20\xE4\xB9\x98\x20\xE4\xB9\x99\x20\xE4\xB9\x9A\x20"  
    "\xE4\xB9\x9B\x20\xE4\xB9\x9C\x20\xE4\xB9\x9D\x20\xE4\xB9\x9E\x20\xE4\xB9\x9F\x20\xE4\xB9\xA0\x20\xE4\xB9\xA1\x20"  
    "\xE4\xB9\xA2\x20\xE4\xB9\xA3\x20\xE4\xB9\xA4\x20\xE4\xB9\xA5\x20\xE4\xB9\xA6\x20\xE4\xB9\xA7\x20\xE4\xB9\xA8\x20"  
    "\xE4\xB9\xA9\x20\xE4\xB9\xAA\x20\xE4\xB9\xAB\x20\xE4\xB9\xAC\x20\xE4\xB9\xAD\x20\xE4\xB9\xAE\x20\xE4\xB9\xAF\x20"  
    "\xE4\xB9\xB0\x20\xE4\xB9\xB1\x20\xE4\xB9\xB2\x20\xE4\xB9\xB3\x20\xE4\xB9\xB4\x20\xE4\xB9\xB5\x20\xE4\xB9\xB6\x20"  
    "\xE4\xB9\xB7\x20\xE4\xB9\xB8\x20\xE4\xB9\xB9\x20\xE4\xB9\xBA\x20\xE4\xB9\xBB\x20\xE4\xB9\xBC\x20\xE4\xB9\xBD\x20"  
    "\xE4\xB9\xBE\x20\xE4\xB9\xBF";  
  
  static const std::string text_thai =  
    "THAI: "  
    "\xE0\xB8\x81\x20\xE0\xB8\x82\x20\xE0\xB8\x83\x20\xE0\xB8\x84\x20\xE0\xB8\x85\x20\xE0\xB8\x86\x20\xE0\xB8\x87\x20"  
    "\xE0\xB8\x88\x20\xE0\xB8\x89\x20\xE0\xB8\x8A\x20\xE0\xB8\x8B\x20\xE0\xB8\x8C\x20\xE0\xB8\x8D\x20\xE0\xB8\x8E\x20"  
    "\xE0\xB8\x8F\x20\xE0\xB8\x90\x20\xE0\xB8\x91\x20\xE0\xB8\x92\x20\xE0\xB8\x93\x20\xE0\xB8\x94\x20\xE0\xB8\x95\x20"  
    "\xE0\xB8\x96\x20\xE0\xB8\x97\x20\xE0\xB8\x98\x20\xE0\xB8\x99\x20\xE0\xB8\x9A\x20\xE0\xB8\x9B\x20\xE0\xB8\x9C\x20"  
    "\xE0\xB8\x9D\x20\xE0\xB8\x9E\x20\xE0\xB8\x9F\x20\xE0\xB8\xA0\x20\xE0\xB8\xA1\x20\xE0\xB8\xA2\x20\xE0\xB8\xA3\x20"  
    "\xE0\xB8\xA4\x20\xE0\xB8\xA5\x20\xE0\xB8\xA6\x20\xE0\xB8\xA7\x20\xE0\xB8\xA8\x20\xE0\xB8\xA9\x20\xE0\xB8\xAA\x20"  
    "\xE0\xB8\xAB\x20\xE0\xB8\xAC\x20\xE0\xB8\xAD\x20\xE0\xB8\xAE\x20\xE0\xB8\xAF\x20\xE0\xB8\xB0\x20\xE0\xB8\xB1\x20"  
    "\xE0\xB8\xB2\x20\xE0\xB8\xB3\x20\xE0\xB8\xB4\x20\xE0\xB8\xB5\x20\xE0\xB8\xB6\x20\xE0\xB8\xB7\x20\xE0\xB8\xB8\x20"  
    "\xE0\xB8\xB9\x20\xE0\xB8\xBA\x20\xE0\xB8\xBF\x20\xE0\xB9\x80\x20\xE0\xB9\x81\x20\xE0\xB9\x82\x20\xE0\xB9\x83\x20"  
    "\xE0\xB9\x84\x20\xE0\xB9\x85\x20\xE0\xB9\x86\x20\xE0\xB9\x87\x20\xE0\xB9\x88\x20\xE0\xB9\x89\x20\xE0\xB9\x8A\x20"  
    "\xE0\xB9\x8B\x20\xE0\xB9\x8C\x20\xE0\xB9\x8D\x20\xE0\xB9\x8E\x20\xE0\xB9\x8F\x20\xE0\xB9\x90\x20\xE0\xB9\x91\x20"  
    "\xE0\xB9\x92\x20\xE0\xB9\x93\x20\xE0\xB9\x94\x20\xE0\xB9\x95\x20\xE0\xB9\x96\x20\xE0\xB9\x97\x20\xE0\xB9\x98\x20"  
    "\xE0\xB9\x99\x20\xE0\xB9\x9A\x20\xE0\xB9\x9B";  
  
  try  
    {  
        auto const host = "echo.websocket.org";  
        auto const port = "443";  
        const std::string test_string =   
#if (SCENARIO != 1)  
          R"crap(432[null,{"commits":[{"guid":"ea65a736-2679-02da-c76b-683330a373f3","urn":"urn:adsk.lynx:commit:ea65a736-2679-02da-c76b-683330a373f3:branch:7b10c530-d031-939b-b867-2442da90add4","changeSet":{"insert":{"autodesk.samples:string_sample.string-1.0.0":{"ASCII:0":{"String":{"guid":"f3638ebf-c44d-4d37-adf6-44bb74597d84","string":"ASCII Simple"}},"ASCII:1":{"String":{"guid":"c5e41ea6-0393-49aa-80ad-8b6d6d6965fa","string":"ASCII Test"}},"ASCII:2":{"String":{"guid":"43354fa0-b864-40a9-a54b-cce2533a60be","string":"ASCII Why so blue,"}},"ASCII:3":{"String":{"guid":"55bb5a52-8f66-44aa-9873-4908c5644ab1","string":"ASCII Panda Bear?"}},"ASCII:4":{"String":{"guid":"ac1a479e-c260-4ad4-80d4-2e2ef2c464ff","string":"ASCII LATIN: ! \" # $ % & ' ( ) * + , - . / 0 1 2 3 4 5 6 7 8 9 : ; < = > ? @ A B C D E F G H I J K L M N O P Q R S T U V W X Y Z [ \\ ] ^ _ ` a b c d e f g h i j k l m n o p q r s t u v w x y z { | } ~"}},"nonASCII:0":{"String":{"guid":"ba6961aa-20b8-48fa-ad57-4e7a5b2105e2","string":)crap"  
          + text_japanese +  
          R"crap(}},"nonASCII:1" : {"String":{"guid":"38fa41e7-47ef-4fdd-8be8-f7e46d5e107d","string":)crap" +  
#endif  
#if (SCENARIO != 2)  
          text_bengali  
#endif  
#if (SCENARIO == 0)  
          +  
#endif  
#if (SCENARIO != 1)  
          R"crap(}},"nonASCII:2":{"String":{"guid":"cbb825a4-8e13-43d5-bbd7-db01c31de904","string":)crap"  
          + text_enclosed_alphanumerics +  
          R"crap(}},"nonASCII:3":{"String":{"guid":"3ada2a0d-5ff2-4724-bfb2-30fae4694e6b","string":)crap"  
          + text_cjk_unified_ideographs +  
          R"crap(}},"nonASCII:4":{"String":{"guid":"0a737e3c-26d9-43b9-9ea3-715ed5b5cb87","string":)crap"  
          + text_thai +  
          R"crap(}}},"autodesk.samples:string_sample.customProperty-1.0.0":{"custom":{"String":{"guid":"4addafc6-ea79-4e9c-94df-c289ce7ad5a5"}}}},"insertTemplates":{"autodesk.samples:string_sample.string-1.0.0":{"inherits":["NamedProperty"],"properties":[{"id":"string","typeid":"String"}],"typeid":"autodesk.samples:string_sample.string-1.0.0"},"autodesk.samples:string_sample.customProperty-1.0.0":{"inherits":["NamedProperty"],"properties":[{"context":"array","id":"arrayOfCustom","typeid":"autodesk.samples:string_sample.string-1.0.0"},{"context":"map","id":"mapOfCustom","typeid":"autodesk.samples:string_sample.string-1.0.0"},{"context":"array","id":"arrayOfString","typeid":"String"},{"context":"map","id":"mapOfString","typeid":"String"}],"typeid":"autodesk.samples:string_sample.customProperty-1.0.0"}}},"meta":{},"sequence":4,"creatorId":"Service:User_Good"}],"container":{"guid":"af60f6d8-7708-710c-dea3-efb8b63d300a","urn":"urn:adsk.lynx:repository:af60f6d8-7708-710c-dea3-efb8b63d300a","creatorId":"Service:User_Good"},"branch":{"guid":"7b10c530-d031-939b-b867-2442da90add4","urn":"urn:adsk.lynx:branch:7b10c530-d031-939b-b867-2442da90add4","creatorId":"Service:User_Good"},"rootCommit":{"guid":"dbc48256-1e5b-5412-e9c9-c9977d2f2a88","urn":"urn:adsk.lynx:commit:dbc48256-1e5b-5412-e9c9-c9977d2f2a88:branch:7b10c530-d031-939b-b867-2442da90add4","sequence":0},"room":{"name":"7b10c530-d031-939b-b867-2442da90add4","properties":{"rootCommit":true,"containerInfo":true,"flatten":true,"roomType":"branch"},"collaborators":[{"guid":"4a61e6d0-310b-c584-71b7-d98a5ea33fa2","type":"human"}]}}])crap"  
#endif  
;  
        // The io_context is required for all I/O  
        boost::asio::io_context ioc;  
  
        // The SSL context is required, and holds certificates  
        ssl::context ctx{ssl::context::sslv23_client};  
  
        // These objects perform our I/O  
        tcp::resolver resolver{ioc};  
        websocket::stream<ssl::stream<tcp::socket>> ws{ioc, ctx};  
  
        ws.next_layer().set_verify_mode(ssl::verify_none);  
  
        // Look up the domain name  
        auto const results = resolver.resolve(host, port);  
  
        // Make the connection on the IP address we get from a lookup  
        boost::asio::connect(ws.next_layer().next_layer(), results.begin(), results.end());  
  
        // Perform the SSL handshake  
        ws.next_layer().handshake(ssl::stream_base::client);  
  
        // Perform the websocket handshake  
        ws.handshake(host, "/");  
  
        // Send the message  
        ws.write(boost::asio::buffer(std::string(test_string)));  
  
        // This buffer will hold the incoming message  
        boost::beast::multi_buffer b;  
  
        // Read a message into our buffer  
        ws.read(b);  
  
        // Close the WebSocket connection  
        ws.close(websocket::close_code::normal);  
  
        // If we get here then the connection is closed gracefully  
  
        // The buffers() function helps print a ConstBufferSequence  
        std::cout << boost::beast::buffers(b.data()) << std::endl;  
    }  
    catch(std::exception const& e)  
    {  
        std::cerr << "Error: " << e.what() << std::endl;  
        return EXIT_FAILURE;  
    }  
    return EXIT_SUCCESS;  
}  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-09-14 22:07:32 UTC  
> Url: https://github.com/boostorg/beast/issues/1245#issuecomment-421496902  

Unfortunately, you're going to need to look into this one yourself. I'll help you. This is the UTF8 validation code:  
https://github.com/boostorg/beast/blob/1da229a27c6f0539d422bcedbcf47cfe2517164a/include/boost/beast/websocket/detail/utf8_checker.hpp#L102  
  
These are the tests for it:  
https://github.com/boostorg/beast/blob/develop/test/beast/websocket/utf8_checker.cpp  
  
What I would suggest, is to write a test which fails for your input data. This should be pretty easy just create an instance of `utf8_checker` and call `write` on it with your buffer. Then call `finish`. Then, trim the input data to the smallest possible string which contains only complete code points and still fails.  
  
Once we have the MVCE (minimum viable compiling example) then we can look at the code together and try to figure out what went wrong.  
  
Thanks

---

## Comment 2

> Username: bsergeev  
> Created at: 2018-09-17 21:23:26 UTC  
> Updated at: 2018-09-18 20:51:11 UTC  
> Url: https://github.com/boostorg/beast/issues/1245#issuecomment-422176549  

... disregard ...

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-09-17 22:34:06 UTC  
> Url: https://github.com/boostorg/beast/issues/1245#issuecomment-422193857  

Oh well if the problem is in the caller that makes things much easier! Especially if it happens in the synchronous implementation (`read_some`). I will explain how the utf8 checker is called. Note that the utf8 checker implements what is called an **online algorithm**[1]. This means that it does not need to have the entire buffer presented at once. Instead, it checks it as it goes.  
  
But what happens when the last code point in an intermediate buffer is incomplete? The checker will save those few bytes for when it gets called again. Those are stored in `utf8_checker_t::cp_[]`, while `utf8_checker_t::need_` tells us how many more characters it needs to have a complete code point.  
  
Here is the code in question:  
```  
if(! rd_utf8_.write(mb) ||  
    (rd_remain_ == 0 && rd_fh_.fin &&  
        ! rd_utf8_.finish()))  
```  
  
And a line by line explanation:  
  
```  
if(! rd_utf8_.write(mb)  
```  
Here we feed the next buffer sequence into the checker. It will return `false` if the input is known to be invalid, `true` otherwise. A return value of `true` doesn't mean it is valid, just that it is not known to be invalid. For example there could be an incomplete code point at the end which becomes invalid on the next received character.  
  
```  
    || (rd_remain_ == 0 && rd_fh_.fin &&  
```  
  
`rd_remain_` is the number of bytes we have left to receive in the _current frame_. When that number reaches zero, it means we have received a complete frame. `rd_fh_.fin` is `true` when the current frame is the final frame of the message. When the expression `rd_remain_ == 0 && rd_fh_.fin` is true, it means that we have received all the data.  
  
```  
&& ! rd_utf8_.finish()))  
```  
Since `&&` short-circuits, the call to `finish()` is only performed when we have received all the data in the message. This performs the final validation on the utf8. Specifically, it checks if there are any leftover bytes in the current code point, and will return `false` if there are leftovers, since an incomplete code point is invalid utf8.  
  
Does this help? If you have specific questions, please post them here.

---

## Comment 4

> Username: bsergeev  
> Created at: 2018-09-18 20:52:55 UTC  
> Url: https://github.com/boostorg/beast/issues/1245#issuecomment-422550380  

I've found the problem and submitted a PR with the fix:  
https://github.com/boostorg/beast/pull/1249  
  
The problem happened when `read_some` buffer ends in the middle of 3-byte symbol (e.g. Bengali). In my example, the first `read_some()` call read 1532 characters, which included the 1st character of the 3-byte sequence, `0xE0`, as the last byte read.  Then, `fail_fast()` was called and filled the last 3 bytes of  `utf8_checker_t::cp_` with `0x81` values, then called `valid()`. Since `{0xE0, 0x81, 0x81, 0x81}` sequence doesn't pass the _overlong_ check for 3-byte values  
```  
(p[0] == 0xe0 && (p[1] & 0xe0) == 0x80) // overlong  
```  
`valid()` returned false and an error was thrown.  
  
Changing the filler values from `0x81` to `0xA0` allows all checks to pass, for 2-, 3-, and probably 4-byte code points (I haven't tested the 4-byte ones, but believe they'll work too).

---

## Comment 5

> Username: vinniefalco  
> Created at: 2018-09-18 21:07:54 UTC  
> Url: https://github.com/boostorg/beast/issues/1245#issuecomment-422555360  

Oh, brilliant! Good job figuring this out, you managed to understand the filler bytes even though it isn't commented.

---

## Comment 6

> Username: hardeagle  
> Created at: 2021-05-22 06:26:32 UTC  
> Updated at: 2021-05-22 06:27:00 UTC  
> Url: https://github.com/boostorg/beast/issues/1245#issuecomment-846359145  

The same problem happend to me, i had solved it by set ws_.  
ws_.binary(true)
