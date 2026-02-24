# #1888 - can not construct span_body [Closed]

> Username: SantinoKeupp  
> Created at: 2020-03-31 08:58:08 UTC  
> Updated at: 2020-04-24 18:01:19 UTC  
> Closed at: 2020-04-24 18:01:19 UTC  
> Url: https://github.com/boostorg/beast/issues/1888  

Hey,  
I want to replace the currently used string_body with a span_body in my request parser, so that I do not have to use dynamic memory. Anyway, I can not construct a request parser (or most likley a span_body) object the way I understand the documentation:  
  
`   
std::array<std::uint8_t, 100> a;  
boost::beast::http::request_parser<boost::beast::http::span_body<std::uint8_t>> req_parser{ a };  
`  
  
Can you please tell me what I am doing wrong?  
  
---  
gcc 9.2.0  
beast version 277

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-03-31 10:19:19 UTC  
> Updated at: 2020-03-31 10:19:54 UTC  
> Url: https://github.com/boostorg/beast/issues/1888#issuecomment-606533556  

Hi @SantinoKeupp ,  
  
I managed to get it compile using the `piecewise_construct` version the the constructor.  
  
Having said, that the output of my test program does not look correct:  
  
https://godbolt.org/z/yube65  
  
I must admit, I've only ever used the empty_body and the allocating bodies in the past.  
I'll speak to @vinniefalco when he comes online and get back to you.  
  
copy of test program here:  
```  
#include <boost/beast/http.hpp>  
#include <array>  
#include <iostream>  
#include <cassert>  
  
int main()  
{  
    namespace http = boost::beast::http;  
    namespace beast = boost::beast;  
    namespace net = beast::net;  
  
  
    std::array<std::uint8_t, 100> a;   
    http::request_parser<http::span_body<std::uint8_t>> req_parser(std::piecewise_construct, std::make_tuple(a.data(), a.size()));  
  
    auto dat = std::string(""  
    "POST / HTTP/1.1\r\n"  
    "target: example.com\r\n"  
    "\r\n\r\n"  
    "hello");  
    beast::error_code ec;  
    req_parser.put(net::buffer(dat), ec);  
    if (ec) { std::cerr << ec.message() << std::endl; std::exit(1); }  
    req_parser.put_eof(ec);  
    if (ec) { std::cerr << ec.message() << std::endl; std::exit(1); }  
    auto&& msg = req_parser.get();  
    auto&& span = msg.body();  
  
    auto payload = std::string(span.begin(), span.end());  
    std::cout << payload << std::endl;  
}  
```

---

## Comment 2

> Username: madmongo1  
> Created at: 2020-03-31 14:29:45 UTC  
> Url: https://github.com/boostorg/beast/issues/1888#issuecomment-606663681  

Hi @SantinoKeupp, here is the corrected code:  
  
```  
#include <boost/beast/http.hpp>  
#include <array>  
#include <iostream>  
#include <cassert>  
  
int main()  
{  
    namespace http = boost::beast::http;  
    namespace beast = boost::beast;  
    namespace net = beast::net;  
  
  
    std::array<std::uint8_t, 100> a;   
    auto req_parser = http::request_parser<  
                        http::span_body<std::uint8_t>>(  
                            std::piecewise_construct,   
                            std::make_tuple(a.data(), a.size()));  
    req_parser.eager(true);  
  
    auto dat = std::string(""  
    "POST / HTTP/1.1\r\n"  
    "Target: example.com\r\n"  
    "Content-Length: 5\r\n"  
    "\r\n"  
    "hello");  
    beast::error_code ec;  
    req_parser.put(net::buffer(dat), ec);  
    if (ec) { std::cerr << ec.message() << std::endl; std::exit(1); }  
    req_parser.put_eof(ec);  
    if (ec) { std::cerr << ec.message() << std::endl; std::exit(1); }  
    auto&& msg = req_parser.get();  
    auto&& span = msg.body();  
  
    auto remaining = span.size();  
    auto consumed = a.size() - span.size();  
    std::cout << consumed << std::endl;  
    auto payload = std::string(a.data(), a.data() + consumed);  
    std::cout << payload << std::endl;  
}  
```  
  
godbolt: https://godbolt.org/z/cpwXnu  
  
Something to note is that the span_body within the parser consumes the internal span as the body is written.  
  
This means that in order to see the body you must:  
  
1. compute the difference between `a.size()` and `req_parser.get().body().size()`  
2. Use `a.data()` and the computed size to determine the bouns of the request's body data.  
  
(see demo code)

---

## Comment 3

> Username: SantinoKeupp  
> Created at: 2020-04-01 07:51:56 UTC  
> Url: https://github.com/boostorg/beast/issues/1888#issuecomment-607092340  

Hey @madmongo1,  
  
piecewise_construct was the missing part. Thank you for that! But now that I think about it, it is quite obvious :)  
The thing with the span_body makes sense if I take a look at the implementatoin, but it is not what I expected. Maybe a hint in the documentation would help here?

---

## Comment 4

> Username: vinniefalco  
> Created at: 2020-04-01 19:09:35 UTC  
> Url: https://github.com/boostorg/beast/issues/1888#issuecomment-607437789  

`span_body` was a mistake

---

## Comment 5

> Username: beckenc  
> Created at: 2020-04-03 14:02:23 UTC  
> Url: https://github.com/boostorg/beast/issues/1888#issuecomment-608450779  

> `span_body` was a mistake  
  
So what would you recommend instead to avoid heavy dynamic memory allocation?  
(Assumption: max body size is known)  
  
vectory_body (preallocated) or buffer_body?

---

## Comment 6

> Username: madmongo1  
> Created at: 2020-04-03 16:40:35 UTC  
> Url: https://github.com/boostorg/beast/issues/1888#issuecomment-608542913  

> > `span_body` was a mistake  
>   
> So what would you recommend instead to avoid heavy dynamic memory allocation?  
> (Assumption: max body size is known)  
>   
> vectory_body (preallocated) or buffer_body?  
  
'mistake' is a broad term. It's reasonable to use the `buffer_body` (even though accessing the data in it is not intuitive). I think what Vinnie means is that although the intentions were good, the design didn't come out as well as planned.

---

## Comment 7

> Username: vinniefalco  
> Created at: 2020-04-04 13:44:35 UTC  
> Url: https://github.com/boostorg/beast/issues/1888#issuecomment-609030897  

> avoid heavy dynamic memory allocation  
  
What's wrong with allocating memory? What problem are you trying to solve? Simple is usually better. Just use `http::string_body` and get on with it.
