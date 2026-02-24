# #2579 - Cannot get http request body from request_parser [Closed]

> Username: UnbearableFate  
> Created at: 2022-12-09 02:45:46 UTC  
> Updated at: 2022-12-10 10:17:10 UTC  
> Closed at: 2022-12-10 10:17:10 UTC  
> Url: https://github.com/boostorg/beast/issues/2579  

boost version :boost_1_80_0    
platform : wsl2 ubuntu 22.04.1    
g++ 12.1.0 in std=c++20 mode    
  
I try to use boost::beast::http::request_parser to parse http request string like this :    
```  
namespace Bhttp = boost::beast::http;  
auto reqStr = conn.receiveMsg(); //get raw string read from socket  
Bhttp::request_parser<Bhttp::string_body> parser;  
boost::beast::error_code ec;  
boost::beast::net::const_buffer cbuffer(reqStr.data(), reqStr.size());  
parser.put(cbuffer, ec);  
if (parser.need_eof()) {  
    parser.put_eof(ec);  
}  
auto pRes = parser.get();  
auto parsedBody = pRes.body();  
```  
I used postman to test my program with all default header and json type body like this:    
![image](https://user-images.githubusercontent.com/6197181/206611145-f8f806ab-0961-4fbf-a3aa-bf9ef123efda.png)  
  
and I checked gdb, it showes that  
raw string read from socket is   
`"POST /company HTTP/1.1\r\nContent-Type: application/json\r\nUser-Agent: PostmanRuntime/7.29.2\r\nAccept: */*\r\nHost: localhost:9877\r\nAccept-Encoding: gzip, deflate, br\r\nConnection: keep-alive\r\nContent-Length: 40\r\n\r\n{\r\n    \"name\": \"tom\",\r\n    \"id\":12314\r\n}"`  
which is a full http request with Content-Length, but `pRes.body()` get null string.    
  
by the way, I can correctly get url from `pRes.target()` and `Content-Length` from `parser.content_length()`.    
so it is not hard to get body by substr last `Content-Length` length stirng from raw request string.    
But I still want to know why `parser.get().body()` cannot work here.  
Thank you.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-12-10 03:48:44 UTC  
> Url: https://github.com/boostorg/beast/issues/2579#issuecomment-1345048714  

It should work. Maybe you need to set `parser.eager( true )` before passing the buffer?  
  
https://www.boost.org/doc/libs/1_80_0/libs/beast/doc/html/beast/ref/boost__beast__http__basic_parser/eager/overload2.html

---

## Comment 2

> Username: UnbearableFate  
> Created at: 2022-12-10 10:17:10 UTC  
> Url: https://github.com/boostorg/beast/issues/2579#issuecomment-1345217077  

It is useful to set parser.eager( true ) before passing the buffer.    
Thank you very much.
