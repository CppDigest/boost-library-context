# #2837 - body_limit exceeded error using coro server example [Closed]

> Username: RavikumarTulugu  
> Created at: 2024-03-14 07:03:54 UTC  
> Updated at: 2024-03-14 14:40:10 UTC  
> Closed at: 2024-03-14 14:40:09 UTC  
> Url: https://github.com/boostorg/beast/issues/2837  

while playing with example https://github.com/boostorg/beast/blob/develop/example/http/server/coro/http_server_coro.cpp , i am running into this body_limit exceeded error. from headers, it seems, the body limit for request is 1 MB. how can I extend the example to handle big request sizes with more than 1 MB.   
body limit exceeded [beast.http:9 at /opensource//base/boost_1_84_0/boost/beast/http/impl/basic_parser.ipp:467:56 in function 'void boost::beast::http::basic_parser<true>::finish_header(boost::beast::error_code &, std::true_type) [isRequest = true]']

---

## Comment 1

> Username: ashtum  
> Created at: 2024-03-14 07:05:35 UTC  
> Url: https://github.com/boostorg/beast/issues/2837#issuecomment-1996698003  

You can use [http::parser::body_limit](https://www.boost.org/doc/libs/1_84_0/libs/beast/doc/html/beast/ref/boost__beast__http__parser/body_limit.html) function.

---

## Comment 2

> Username: RavikumarTulugu  
> Created at: 2024-03-14 14:15:42 UTC  
> Url: https://github.com/boostorg/beast/issues/2837#issuecomment-1997561420  

Can you pls point to sample usage code ? I tried adding the below line, but it was throwing compile errors. like below.   
 http::parser<true, http::string_body>::body_limit ( 64 * 1024 * 1024 );  
compile error :   
238:42: error: call to non-static member function without an object argument  
  http::parser<true, http::string_body>::body_limit ( 64 * 1024 * 1024 );  
  ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~^~~~~~~~~~

---

## Comment 3

> Username: ashtum  
> Created at: 2024-03-14 14:22:55 UTC  
> Url: https://github.com/boostorg/beast/issues/2837#issuecomment-1997577132  

`body_limit` is a member function; you should call it on an instance of `http::parser`. You can find how it is used in this example:  
https://github.com/boostorg/beast/blob/develop/example/advanced/server/advanced_server.cpp#L374

---

## Comment 4

> Username: RavikumarTulugu  
> Created at: 2024-03-14 14:40:09 UTC  
> Url: https://github.com/boostorg/beast/issues/2837#issuecomment-1997613942  

I looked at the advanced_server.cpp example and it seems the 2 examples differ huge in code. we were using the coro server as base to build our server. i was  expecting it to be a single line change. ok , closing the bug.
