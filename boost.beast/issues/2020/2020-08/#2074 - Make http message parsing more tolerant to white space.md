# #2074 - Make http message parsing more tolerant to white space [Closed]

> Username: RicardoCapurro  
> Created at: 2020-08-28 20:09:11 UTC  
> Updated at: 2020-08-28 20:29:49 UTC  
> Closed at: 2020-08-28 20:29:49 UTC  
> Url: https://github.com/boostorg/beast/issues/2074  

When parsing http messages, while reading the "Name: Value" fields, beast rejects when white space is present between the Name and the ':'  
  
This is an example  
  
```  
#include <string>  
#include <boost/asio.hpp>  
#include <boost/beast.hpp>  
  
void main(int, const char**) {  
    std::string msg = "HTTP/1.1 200 OK\r\n"  
                      "Content-Length: 6\r\n"  
                      "Content-Type: text/html; charset=utf-8\r\n"  
                      "Invalid-name-with-trailing-space : valiuer\n" // Here is the problem, note the space before :  
                      "Date: Tue, 18 Aug 2020 19:39:10 GMT\r\n"  
                      "\r\n"  
                      "123456";  
    boost::beast::http::response_parser<boost::beast::http::string_body> parser;  
    parser.eager(true);  
    boost::beast::error_code ec;  
    parser.put(boost::asio::buffer(msg), ec); // Parsing fails!  
    assert( !ec.failed() );  
}  
  
```  
  
Sure this is a bad response sent by a http server, but although it is not very common it happens, and we not always have control on the http server we communicate with to fix that.  
And most (if not all) of common web browsers support responses of this kind!  
  
So my idea is to fix this on beast and support this kind of responses  
  
I can provode the PR with my proposed change

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-08-28 20:11:42 UTC  
> Url: https://github.com/boostorg/beast/issues/2074#issuecomment-683126738  

@vinniefalco what do we think about this?  
We’ve discussed a permissive option before and held off because of lack of proven demand.  
  
@RicardoCapurro can you provide an example of an actual live server that is causing your application to fail?

---

## Comment 2

> Username: RicardoCapurro  
> Created at: 2020-08-28 20:19:05 UTC  
> Url: https://github.com/boostorg/beast/issues/2074#issuecomment-683129433  

Here is my proposed change

---

## Comment 3

> Username: RicardoCapurro  
> Created at: 2020-08-28 20:19:11 UTC  
> Url: https://github.com/boostorg/beast/issues/2074#issuecomment-683129475  

https://github.com/RicardoCapurro/beast/pull/1

---

## Comment 4

> Username: madmongo1  
> Created at: 2020-08-28 20:21:19 UTC  
> Url: https://github.com/boostorg/beast/issues/2074#issuecomment-683130311  

Thank you, but I hope you understand that beast has been used in production in many systems for 4 years and we have never had the need to accept malformed headers.  
  
Do you have an example of a specific web server or client that is sending these malformed headers, or is this a theoretical concern?

---

## Comment 5

> Username: RicardoCapurro  
> Created at: 2020-08-28 20:26:57 UTC  
> Url: https://github.com/boostorg/beast/issues/2074#issuecomment-683132436  

The problem happened accessing a private web server, no access is available from the Internet.  
But normal web browsers accept those responses without any problems.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2020-08-28 20:29:44 UTC  
> Url: https://github.com/boostorg/beast/issues/2074#issuecomment-683133442  

Nope. Beast only accepts compliant HTTP and there are no plans to change that.
