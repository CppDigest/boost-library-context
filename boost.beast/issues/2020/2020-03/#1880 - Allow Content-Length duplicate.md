# #1880 - Allow Content-Length duplicate [Closed]

> Username: Dicrit  
> Created at: 2020-03-18 11:31:41 UTC  
> Updated at: 2020-03-21 15:16:43 UTC  
> Closed at: 2020-03-21 15:16:43 UTC  
> Url: https://github.com/boostorg/beast/issues/1880  

According to [rfc7230 section-3.3.2](https://tools.ietf.org/html/rfc7230#section-3.3.2):  
"If a message is received that has multiple Content-Length header fields with field-values consisting of the same decimal value, or a single Content-Length header field with a field value containing a list of identical decimal values (e.g., "Content-Length: 42, 42"), indicating that duplicate Content-Length header fields have been generated or combined by an upstream message processor, then the recipient MUST **either reject the message as invalid or replace the duplicated field-values** with a single valid Content-Length field containing that decimal value prior to determining the message body length or forwarding the message."  
I've got one response with duplicated Content-Length: 0. Browsers allowed it, but my program - didn't. So is it possible to specify how to react on Content-Length duplicates?  
Following example contains real headers that I dumped from browser:  
```  
#include <boost/beast/http.hpp>  
  
int main(int argc, char* argv[])  
{  
    const char header[] =  
        "HTTP/1.1 200 OK\r\n"  
        "Server: nginx\r\n"  
        "Date: Wed, 11 Mar 2020 17:18:19 GMT\r\n"  
        "Content-Type: application/octet-stream\r\n"  
        "Content-Length: 0\r\n"  
        "Connection: keep-alive\r\n"  
        "Access-Control-Allow-Origin: *\r\n"  
        "Access-Control-Allow-Methods: GET, OPTIONS, POST\r\n"  
        "Access-Control-Allow-Headers: Authorization, Content-Type, Accept\r\n"  
        "Access-Control-Max-Age: 1728000\r\n"  
        "Access-Control-Allow-Credentials: true\r\n"  
        "Content-Length: 0\r\n"  
        "Content-Type: text/plain\r\n"  
        "\r\n";  
  
    boost::beast::http::response_parser<boost::beast::http::empty_body> parser;  
    boost::beast::error_code ec;  
    parser.put(boost::asio::buffer(header, sizeof(header) - 1), ec);  
    //ec = boost::beast::http::error::bad_content_length  
}  
```

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-03-18 11:46:58 UTC  
> Updated at: 2020-03-18 11:47:13 UTC  
> Url: https://github.com/boostorg/beast/issues/1880#issuecomment-600577034  

Thanks for creating the issue. I'll look into this right away.

---

## Comment 2

> Username: madmongo1  
> Created at: 2020-03-18 20:12:51 UTC  
> Updated at: 2020-03-18 20:13:20 UTC  
> Url: https://github.com/boostorg/beast/issues/1880#issuecomment-600836038  

Hi @Dicrit,  
  
I thought this might be a blocker for your project so I have expedited a version release candidate.  
  
There is a branch with correct behaviour in my repo, pending my PR acceptance into develop branch.  
  
https://github.com/madmongo1/beast/tree/content-length-fix-1880
