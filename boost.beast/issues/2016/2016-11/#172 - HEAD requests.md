# #172 - HEAD requests [Closed]

> Username: dirkvdb  
> Created at: 2016-11-07 22:49:12 UTC  
> Updated at: 2016-11-12 17:42:46 UTC  
> Closed at: 2016-11-12 17:42:46 UTC  
> Url: https://github.com/boostorg/beast/issues/172  

I'm trying to do a HEAD request using beast.  
Reading the response gives an error: unexpected end of data  
  
Response data:  
HTTP/1.1 200 OK\r\n  
SERVER: Darwin/15.4.0, UPnP/1.0\r\n  
CONTENT-LENGTH: 15\r\n  
CONTENT-TYPE: plain/text\r\n  
\r\n  
  
It seems wrong to give an error when responding to a HEAD request, since no data should be in the response.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2016-11-07 22:56:09 UTC  
> Updated at: 2016-11-07 22:57:49 UTC  
> Url: https://github.com/boostorg/beast/issues/172#issuecomment-258990339  

Beast uses free functions to send and receive HTTP messages, so it doesn't "know" that the request you sent was a HEAD request, as there is no state maintained between the call to write the request and read the response. When Beast receives the response, it sees "Content-Length: 15" and assumes that it is going to see a body. You can use `beast::http::parse` with an instance of `beast::http::parser_v1` with the `skip_body` option set to `true`. Sample code to do so would look like this:  
  
```  
using namespace beast::http;  
streambuf sb;  
parser_v1<false, empty_body, headers> p;  
p.set_option(skip_body{true});  
parse(socket, sb, p);  
message<false, empty_body, headers> res = p.release(); // the response is in 'res'  
```  
  
I will add additional overloads of `read` and `async_read` to make it easier to read into a `message_headers` object, see #173

---

## Comment 2

> Username: vinniefalco  
> Created at: 2016-11-07 23:20:18 UTC  
> Url: https://github.com/boostorg/beast/issues/172#issuecomment-258995550  

In **1.0.0-b19** there will be another choice (from #173):  
  
```  
using namespace beast::http;  
streambuf sb;  
response_headers res;  
read(socket, sb, res); // read a response to HEAD request  
```  
  
Is this suitable?

---

## Comment 3

> Username: dirkvdb  
> Created at: 2016-11-07 23:33:10 UTC  
> Updated at: 2016-11-07 23:33:40 UTC  
> Url: https://github.com/boostorg/beast/issues/172#issuecomment-258998114  

Yes, that looks good. I will try out the commit tomorrow. (Past midnight here :-)  
  
Thanks for the fast response!

---

## Comment 4

> Username: vinniefalco  
> Created at: 2016-11-07 23:34:44 UTC  
> Url: https://github.com/boostorg/beast/issues/172#issuecomment-258998453  

This branch has the changes for `read` to work with `message_headers`  
https://github.com/vinniefalco/Beast/tree/headers

---

## Comment 5

> Username: dirkvdb  
> Created at: 2016-11-08 19:09:38 UTC  
> Url: https://github.com/boostorg/beast/issues/172#issuecomment-259229551  

It works fine, thanks!
