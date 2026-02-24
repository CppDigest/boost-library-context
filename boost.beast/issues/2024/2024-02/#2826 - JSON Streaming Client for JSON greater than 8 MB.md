# #2826 - JSON Streaming Client for JSON greater than 8 MB [Closed]

> Username: composition  
> Created at: 2024-02-21 16:01:00 UTC  
> Updated at: 2024-02-21 18:28:23 UTC  
> Closed at: 2024-02-21 18:28:23 UTC  
> Url: https://github.com/boostorg/beast/issues/2826  

### Version of Beast  
1.84  
  
### Steps necessary to reproduce the problem  
* Create a JSON serving HTTP server using [this example](https://www.boost.org/doc/libs/master/libs/beast/example/http/server/small/http_server_small.cpp) as the template.  
* Create an HTTP client using [this example](https://www.boost.org/doc/libs/master/libs/beast/example/http/client/async/http_client_async.cpp)  
    *  Use [json_body](https://www.boost.org/doc/libs/1_81_0/libs/beast/example/http/client/body/json_body.hpp) as the response body instead of `http::string_body`  
    * Basically `http::response<json_body> res_` instead of `http::response<http::string_body> res_`  
      
### All relevant compiler information  
* gcc-11  
  
### Problem  
* Cannot receive json over 8 MB, which is expected, since the default `body_limit` is set to 8 MB.  
* [This answer](https://stackoverflow.com/a/58592247/3022897) on SO suggests to override the `body_limit` in the parser using [`body_limit`](https://www.boost.org/doc/libs/1_84_0/libs/beast/doc/html/beast/ref/boost__beast__http__parser/body_limit.html) API.  
* But the parser in boost.json (the one used in `json_body` above) does not have such a method.  
* How can I update the `body_limit` in this case?

---

## Comment 1

> Username: ashtum  
> Created at: 2024-02-21 16:08:44 UTC  
> Url: https://github.com/boostorg/beast/issues/2826#issuecomment-1957126744  

You need to set the `body_limit` on [`http::request_parser`](https://www.boost.org/doc/libs/1_84_0/libs/beast/doc/html/beast/ref/boost__beast__http__request_parser.html).

---

## Comment 2

> Username: composition  
> Created at: 2024-02-21 16:13:34 UTC  
> Updated at: 2024-02-21 16:19:04 UTC  
> Url: https://github.com/boostorg/beast/issues/2826#issuecomment-1957149334  

Thanks @ashtum - how do I get hold of [http::request_parser](https://www.boost.org/doc/libs/1_84_0/libs/beast/doc/html/beast/ref/boost__beast__http__request_parser.html) on the [client side](https://www.boost.org/doc/libs/master/libs/beast/example/http/client/async/http_client_async.cpp).  
  
Note that I am using [json::stream_parser](https://www.boost.org/doc/libs/1_81_0/boost/json/stream_parser.hpp).

---

## Comment 3

> Username: sehe  
> Created at: 2024-02-21 16:20:07 UTC  
> Updated at: 2024-02-21 16:21:20 UTC  
> Url: https://github.com/boostorg/beast/issues/2826#issuecomment-1957173030  

You don't. Just replace the response object:   
  
```c++  
http::response_parser<http::string_body> res_;  
```  
  
with later:  
  
```c++  
// Receive the HTTP response  
res_.body_limit(16<<20); // 16 MiB  
http::async_read(stream_, buffer_, res_,  
    beast::bind_front_handler(  
        &session::on_read,  
        shared_from_this()));  
```  
  
 and  
  
```c++  
// Write the message to standard out  
std::cout << res_.get() << std::endl;  
```

---

## Comment 4

> Username: composition  
> Created at: 2024-02-21 16:22:49 UTC  
> Url: https://github.com/boostorg/beast/issues/2826#issuecomment-1957182983  

Thanks @sehe . I am intentionally avoiding the approach above because I don't want to first receive JSON in a `string_body` and then create a `json::value` from it.

---

## Comment 5

> Username: sehe  
> Created at: 2024-02-21 16:26:04 UTC  
> Updated at: 2024-02-21 16:29:41 UTC  
> Url: https://github.com/boostorg/beast/issues/2826#issuecomment-1957194839  

> Note that I am using [json::stream_parser](https://www.boost.org/doc/libs/1_81_0/boost/json/stream_parser.hpp).  
  
If so, please show the relevant code, since none of the examples deal with `stream_parser`, except if you use a custom body type, as in the example. In that case, you still use the http message parser exactly the same way as I just showed using the example that you linked.  
  
The example seems hard to find in the docs, but here it lives https://github.com/boostorg/beast/tree/develop/example/http/client/body

---

## Comment 6

> Username: ashtum  
> Created at: 2024-02-21 16:26:24 UTC  
> Updated at: 2024-02-21 16:30:56 UTC  
> Url: https://github.com/boostorg/beast/issues/2826#issuecomment-1957195935  

Actually there are two overloads of `http::async_read`, one of them takes a reference to the parser instead of http message: https://www.boost.org/doc/libs/1_84_0/libs/beast/doc/html/beast/ref/boost__beast__http__async_read/overload1.html  
Using this interface gives you more control over the parser object. `advanced_server` example is good guide for how to use a parser with `http::async_read` interface: https://github.com/boostorg/beast/blob/develop/example/advanced/server/advanced_server.cpp#L374

---

## Comment 7

> Username: composition  
> Created at: 2024-02-21 16:52:58 UTC  
> Updated at: 2024-02-21 17:59:09 UTC  
> Url: https://github.com/boostorg/beast/issues/2826#issuecomment-1957285312  

@sehe - Pls check my first comment. I am using the exact same [example](https://github.com/boostorg/beast/tree/develop/example/http/client/body) 😃 . [This](https://github.com/boostorg/beast/blob/develop/example/http/client/body/json_client.cpp#L47) is the declaration that I am using for the response object. My problem boils down to not being able to set the `body_limit` in this example.  
  
@ashtum - While I can use the other overload that takes a reference to the parser, the problem is [json::stream_parser](https://www.boost.org/doc/libs/1_81_0/boost/json/stream_parser.hpp) cannot convey to underlying `http::parser` to change the default body limit.

---

## Comment 8

> Username: grisumbras  
> Created at: 2024-02-21 17:28:11 UTC  
> Url: https://github.com/boostorg/beast/issues/2826#issuecomment-1957403831  

I think, this should be relevant: https://www.boost.org/doc/libs/1_84_0/libs/beast/doc/html/beast/using_http/parser_stream_operations/read_large_response_body.html

---

## Comment 9

> Username: composition  
> Created at: 2024-02-21 17:58:30 UTC  
> Url: https://github.com/boostorg/beast/issues/2826#issuecomment-1957535232  

thx @grisumbras - read that piece of code. I am stuck cuz the parser I need to use does not support setting the `body_limit`... So looking for advice on how to modify json::stream_parser or if there is an alternate way to set body_limit or something else...

---

## Comment 10

> Username: ashtum  
> Created at: 2024-02-21 18:10:38 UTC  
> Url: https://github.com/boostorg/beast/issues/2826#issuecomment-1957578322  

@composition You don't need to touch the internal `json::stream_parser` you should modify the http response parser, in that specific example it would be:  
```C++  
    http::response_parser<json_body> parser;  
    parser.body_limit(100'000'000);  
  
    // Receive the HTTP response  
    http::read(stream, buffer, parser);  
  
    // Write the message to standard out  
    std::cout << parser.release() << std::endl;  
```

---

## Comment 11

> Username: composition  
> Created at: 2024-02-21 18:27:22 UTC  
> Url: https://github.com/boostorg/beast/issues/2826#issuecomment-1957636224  

> @composition You don't need to touch the internal `json::stream_parser` you should modify the http response parser, in that specific example it would be:  
>   
> ```c++  
>     http::response_parser<json_body> parser;  
>     parser.body_limit(100'000'000);  
>   
>     // Receive the HTTP response  
>     http::read(stream, buffer, parser);  
>   
>     // Write the message to standard out  
>     std::cout << parser.release() << std::endl;  
> ```  
  
awesome.. thx!
