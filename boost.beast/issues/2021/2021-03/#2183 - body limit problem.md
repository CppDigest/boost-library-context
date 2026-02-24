# #2183 - body limit problem [Closed]

> Username: xhcoding  
> Created at: 2021-03-08 11:49:29 UTC  
> Updated at: 2022-01-09 05:17:21 UTC  
> Closed at: 2022-01-09 05:17:21 UTC  
> Url: https://github.com/boostorg/beast/issues/2183  

boost version: 1.75  
  
```c++  
  
    {  
        RequestParser parser;  
  
        boost::beast::multi_buffer b;  
        boost::beast::ostream(b) << "POST / HTTP/1.1\r\n"  
                                    "\r\n"  
                                    "0123456789"  
                                    "0123456789"  
                                    "0123456789"  
                                    "0123456789";  
        parser.body_limit(1);  
        parser.eager(true);  
        boost::beast::error_code ec;  
        parser.put(b.data(), ec);  
        CHECK(ec == boost::beast::http::error::body_limit);  
    }  
    {  
        ResponseParser parser;  
  
        boost::beast::multi_buffer b;  
        boost::beast::ostream(b) << "HTTP/1.1 200 OK\r\n"  
                                    "\r\n"  
                                    "0123456789"  
                                    "0123456789"  
                                    "0123456789"  
                                    "0123456789";  
        parser.body_limit(1);  
        parser.eager(true);  
        boost::beast::error_code ec;  
        parser.put(b.data(), ec);  
        CHECK(ec == boost::beast::http::error::body_limit);  
    }  
```  
  
output:  
``` shell  
  CHECK( ec == boost::beast::http::error::body_limit )  
with expansion:  
  system:0 == 9  
  
===============================================================================  
test cases: 1 | 1 failed  
assertions: 2 | 1 passed | 1 failed  
```  
  
  
Why is `error_code` of `RequestParser` equal to `success` , but `ResponseParser` is `body_limit`

---

## Comment 1

> Username: madmongo1  
> Created at: 2021-03-08 12:20:36 UTC  
> Url: https://github.com/boostorg/beast/issues/2183#issuecomment-792719687  

It looks like we're not respecting the body_limit. That's odd.  
  
Are you able to share the complete test?

---

## Comment 2

> Username: xhcoding  
> Created at: 2021-03-09 03:38:28 UTC  
> Url: https://github.com/boostorg/beast/issues/2183#issuecomment-793332252  

test code:  
```  
#include <boost/beast.hpp>  
#include <iostream>  
  
int main(int argc, char *argv[]) {  
  {  
    boost::beast::http::request_parser<boost::beast::http::dynamic_body> parser;  
    boost::beast::multi_buffer b;  
    boost::beast::ostream(b) << "POST / HTTP/1.1\r\n"  
                                "\r\n"  
                                "0123456789"  
                                "0123456789"  
                                "0123456789"  
                                "0123456789";  
    parser.body_limit(1);  
    parser.eager(true);  
    boost::beast::error_code ec;  
    parser.put(b.data(), ec);  
  
    std::cout << ec << ": " << ec.message() << std::endl;  
  }  
  
  {  
    boost::beast::http::response_parser<boost::beast::http::dynamic_body> parser;  
    boost::beast::multi_buffer b;  
  
     boost::beast::ostream(b) << "HTTP/1.1 200 OK\r\n"  
                                 "\r\n"  
                                 "0123456789"  
                                 "0123456789"  
                                 "0123456789"  
                                 "0123456789";  
    parser.body_limit(1);  
    parser.eager(true);  
    boost::beast::error_code ec;  
    parser.put(b.data(), ec);  
  
    std::cout << ec << ": " << ec.message() << std::endl;  
  }  
  
  return 0;  
  
}  
  
```  
  
output:  
```  
system:0: 操作成功完成。  
beast.http:9: body limit exceeded  
```  
  
os: `Windows10`  
compiler:    ` msvc x86_64`

---

## Comment 3

> Username: stale[bot]  
> Created at: 2021-05-29 16:10:57 UTC  
> Url: https://github.com/boostorg/beast/issues/2183#issuecomment-850857861  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 4

> Username: stale[bot]  
> Created at: 2022-01-09 05:16:55 UTC  
> Url: https://github.com/boostorg/beast/issues/2183#issuecomment-1008232404  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
