# #281 - http::write do not send message unless i close socket [Closed]

> Username: zhaohuaxishi  
> Created at: 2017-03-16 08:55:32 UTC  
> Updated at: 2017-03-17 05:45:33 UTC  
> Closed at: 2017-03-17 05:45:33 UTC  
> Url: https://github.com/boostorg/beast/issues/281  

i use beast to write http server and client, my client block if i do not close the sock on server side.  
  
on client side, i send http request and wait response like this:  
  
```  
RequestType request;  
// setup request  
beast::http::write(sock, request);  
  
beast::streambuf buffer;  
ResponseType resp;  
// setup response  
beast::http::read(sock, buffer, resp);    // this function block  
```  
  
on server side, i read request and write response like this:  
  
```  
RequestType request;  
beast::http::read(sock, buffer, request);  
  
auto response = handle_request(request);  
beast::http::write(sock, response);  
// sock->close();  
```  
  
if i do not call `sock->close()` on server side,  client call of `beast::http::read(sock, buffer, resp)` will block.  
  
Any help would be appreciated.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-03-16 11:27:24 UTC  
> Updated at: 2017-03-16 11:27:49 UTC  
> Url: https://github.com/boostorg/beast/issues/281#issuecomment-287029817  

Are you specifying HTTP/1.0 as the version? Can you show me exactly what your request and response looks like (i.e. the code to set up the messages)?

---

## Comment 2

> Username: zhaohuaxishi  
> Created at: 2017-03-16 11:56:52 UTC  
> Url: https://github.com/boostorg/beast/issues/281#issuecomment-287035741  

request looks like this:  
```  
beast::http::request<beast::http::string_body> context_request;  
context_request.version = 11;  
context_request.method = "SETUP";  
context_request.url = "/context";  
context_request.fields.insert("name", "beast");  
```  
  
and response looks like this:  
```  
beast::http::response<beast::http::string_body> resp;  
resp.version = request.version;  
resp.status = 200;  
```  
  
and thank you for help @vinniefalco

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-03-16 12:05:59 UTC  
> Updated at: 2017-03-16 12:06:17 UTC  
> Url: https://github.com/boostorg/beast/issues/281#issuecomment-287037458  

HTTP responses need a Content-Length for 200 level status code, otherwise the end of message is indicated by the end of file received when the socket is closed. Try:  
```  
resp.fields.insert("Content-Length", "0");  
```  
alternatively you could try  
```  
beast::http::prepare(resp);  
```  
Before calling `write`. See:  
http://vinniefalco.github.io/beast/beast/ref/http__prepare.html

---

## Comment 4

> Username: zhaohuaxishi  
> Created at: 2017-03-16 12:26:37 UTC  
> Url: https://github.com/boostorg/beast/issues/281#issuecomment-287041753  

it works! thank you very much. but if i define response like this:  
```  
beast::http::response<beast::http::empty_body> resp;  
```  
`beast::http::read` will complain about `Body has no reader`, is that means i can not use `beast::http::empty_body` to instance `beast::http::response`, if not, then how should i implement the reader

---

## Comment 5

> Username: vinniefalco  
> Created at: 2017-03-16 14:57:42 UTC  
> Url: https://github.com/boostorg/beast/issues/281#issuecomment-287083759  

1. `empty_body` was removed a few versions ago, you should update to the tip of the **master** branch  
  
2. Use `beast::http::response_header` if you are not expecting a body..

---

## Comment 6

> Username: zhaohuaxishi  
> Created at: 2017-03-17 05:45:33 UTC  
> Url: https://github.com/boostorg/beast/issues/281#issuecomment-287272438  

Got it, again thanks for help.
