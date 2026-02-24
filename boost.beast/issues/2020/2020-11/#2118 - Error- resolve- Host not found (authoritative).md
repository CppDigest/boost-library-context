# #2118 - Error: resolve: Host not found (authoritative) [Closed]

> Username: Evgengrmit  
> Created at: 2020-11-08 07:27:23 UTC  
> Updated at: 2020-11-09 09:58:37 UTC  
> Closed at: 2020-11-09 09:58:37 UTC  
> Url: https://github.com/boostorg/beast/issues/2118  

I'm learning to use the boost beast library. I already wrote the server. It works perfectly.I also have a client sending POST requests. I'm starting the server.However, I can't send him a request to the URL http://localhost:8080, but in response, the console outputs: Error: resolve: Host not found (authoritative) The code is written based on: https://github.com/boostorg/beast/blob/develop/example/http/client/sync/http_client_sync.cpp  
```  
#include <boost/asio/connect.hpp>  
#include <boost/asio/ip/tcp.hpp>  
#include <boost/beast/core.hpp>  
#include <boost/beast/http.hpp>  
#include <boost/beast/version.hpp>  
#include <cstdlib>  
#include <iostream>  
#include <string>  
  
namespace beast = boost::beast;  // from <boost/beast.hpp>  
namespace http = beast::http;    // from <boost/beast/http.hpp>  
namespace net = boost::asio;     // from <boost/asio.hpp>  
using tcp = net::ip::tcp;        // from <boost/asio/ip/tcp.hpp>  
  
int Client(int argc, char* argv[]) {  
  if (argc != 5 && argc != 6) {  
    std::cerr << "Usage: http-client-sync <host> <port> <target> "  
                 "<request>[<HTTP version: 1.0 or 1.1(default)>]\n"  
              << "Example:\n"  
              << "    http-client-sync www.example.com 80 /v1/api/suggest {\"input\": \"<user_input>\"}\n"  
              << "    http-client-sync www.example.com 80 / 1.0\n";  
    return EXIT_FAILURE;  
  }  
  try {  
  
    auto const host = argv[1];  
    auto const port = argv[2];  
    auto const target = argv[3];  
    auto const request_body = argv[4];  
    int version = argc == 5 && !std::strcmp("1.0", argv[5]) ? 10 : 11;  
std::cout <<host << std::endl;  
  
    net::io_context ioc;  
  
  
    tcp::resolver resolver{ioc};  
    beast::tcp_stream stream(ioc);  
  
  
    auto const results = resolver.resolve(host, port);  
  
    stream.connect(results);  
  
    http::string_body::value_type body = request_body;  
    http::request<http::string_body> req{http::verb::post, target, version};  
    req.set(http::field::host, host);  
    req.set(http::field::body, body);  
    req.set(http::field::user_agent, BOOST_BEAST_VERSION_STRING);  
    req.set(http::field::content_type, "application/json");  
  
    http::write(stream, req);  
  
    boost::beast::flat_buffer buffer;  
  
    http::response<http::string_body> res;  
  
    http::read(stream, buffer, res);  
  
  
    std::cout << res << std::endl;  
  
  
    beast::error_code ec;  
    stream.socket().shutdown(tcp::socket::shutdown_both, ec);  
  
    if(ec && ec != beast::errc::not_connected)  
      throw beast::system_error{ec};  
  
  } catch (std::exception const& e) {  
    std::cerr << "Error: " << e.what() << std::endl;  
    return EXIT_FAILURE;  
  }  
  return EXIT_SUCCESS;  
}  
int main(int argc, char* argv[]){  
   Client(argc,argv);  
  return 0;  
}  
```

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-11-08 07:56:03 UTC  
> Url: https://github.com/boostorg/beast/issues/2118#issuecomment-723543130  

What are you entering on the command line?

---

## Comment 2

> Username: Evgengrmit  
> Created at: 2020-11-08 08:15:53 UTC  
> Url: https://github.com/boostorg/beast/issues/2118#issuecomment-723544660  

Good morning!  
I use in command line:  
```  
./cmake-build-debug/tests http://localhost:8080 80 /v1/api/suggest \{"input": "hel"\}  
```  
when I use cURL I enter:  
```  
curl --location --request POST 'http://localhost:8080/v1/api/suggest' \  
--header 'Content-Type: text/plain' \  
--data-raw '{"input": "hel"}'  
```  
Сorrect answer:  
```  
{  
    "suggestions": [  
        {  
            "position": 0,  
            "text": "hello my world"  
        },  
        {  
            "position": 1,  
            "text": "hello"  
        },  
        {  
            "position": 2,  
            "text": "hello world"  
        },  
        {  
            "position": 3,  
            "text": "helm"  
        }  
    ]  
}  
```

---

## Comment 3

> Username: madmongo1  
> Created at: 2020-11-08 14:43:25 UTC  
> Url: https://github.com/boostorg/beast/issues/2118#issuecomment-723586639  

I don't see any code in your program to break down a url into its components.  
  
Try this:  
  
```  
./cmake-build-debug/tests localhost 8080 /v1/api/suggest \{"input": "hel"\}  
```

---

## Comment 4

> Username: Evgengrmit  
> Created at: 2020-11-09 09:58:33 UTC  
> Url: https://github.com/boostorg/beast/issues/2118#issuecomment-723904951  

Thanks! This was the solution to one of the problems. I still had to work with the request body. Thank you again.
