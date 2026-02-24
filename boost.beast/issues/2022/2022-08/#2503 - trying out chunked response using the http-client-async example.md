# #2503 - trying out chunked response using the http-client-async example. [Closed]

> Username: ashjas  
> Created at: 2022-08-17 13:45:44 UTC  
> Updated at: 2022-08-18 09:58:57 UTC  
> Closed at: 2022-08-18 09:58:57 UTC  
> Url: https://github.com/boostorg/beast/issues/2503  

### Version of Beast  
1.71  
  
I am trying to get response from this api: http://jigsaw.w3.org/HTTP/ChunkedScript  
  
using the http-client-async example, i get the following response:  
  
```http-client-async jigsaw.w3.org 80 HTTP/ChunkedScript```  
  
```  
HTTP/1.1 400 Bad Request  
Server: cloudflare  
Date: Wed, 17 Aug 2022 13:39:21 GMT  
Content-Type: text/html  
Content-Length: 155  
Connection: close  
CF-RAY: -  
  
<html>  
<head><title>400 Bad Request</title></head>  
<body>  
<center><h1>400 Bad Request</h1></center>  
<hr><center>cloudflare</center>  
</body>  
</html>  
```  
  
Is there anything extra that needs to be handled for chunked encoding from the [http async example](https://github.com/boostorg/beast/blob/develop/example/http/client/async/http_client_async.cpp) ?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-08-17 13:48:28 UTC  
> Url: https://github.com/boostorg/beast/issues/2503#issuecomment-1218035712  

The target has to be an absolute path (start with '/'). Use this command line:  
```  
http-client-async jigsaw.w3.org 80 /HTTP/ChunkedScript  
```

---

## Comment 2

> Username: ashjas  
> Created at: 2022-08-17 13:56:37 UTC  
> Url: https://github.com/boostorg/beast/issues/2503#issuecomment-1218046017  

Thanks, but i get this now:  
```  
HTTP/1.1 301 Moved Permanently  
Date: Wed, 17 Aug 2022 13:55:40 GMT  
Transfer-Encoding: chunked  
Connection: keep-alive  
Cache-Control: max-age=3600  
Expires: Wed, 17 Aug 2022 14:55:40 GMT  
Location: https://jigsaw.w3.org/HTTP/ChunkedScript  
Server: cloudflare  
CF-RAY: 73c2e8a4d85fb29d-MAA  
alt-svc: h3=":443"; ma=86400, h3-29=":443"; ma=86400  
  
0  
  
0  
  
  
```

---

## Comment 3

> Username: vinniefalco  
> Created at: 2022-08-17 13:58:08 UTC  
> Updated at: 2022-08-17 13:58:19 UTC  
> Url: https://github.com/boostorg/beast/issues/2503#issuecomment-1218047940  

So, that's a "redirect." You need to perform the request again, but to the destination indicated by the "Location" field:  
```  
Location: https://jigsaw.w3.org/HTTP/ChunkedScript  
```  
  
This is HTTPS so you will need to use the http-client-async-ssl example:  
https://github.com/boostorg/beast/tree/develop/example/http/client/async-ssl  
  
@madmongo1

---

## Comment 4

> Username: ashjas  
> Created at: 2022-08-17 14:13:08 UTC  
> Url: https://github.com/boostorg/beast/issues/2503#issuecomment-1218067468  

Oh ic..  
is there any chunked url for testing that serves data on http ?

---

## Comment 5

> Username: sehe  
> Created at: 2022-08-17 14:31:39 UTC  
> Url: https://github.com/boostorg/beast/issues/2503#issuecomment-1218091914  

@ashjas try https://jigsaw.w3.org/HTTP/ChunkedScript (or similar online test services, like httpbin.org)

---

## Comment 6

> Username: madmongo1  
> Created at: 2022-08-17 20:28:04 UTC  
> Updated at: 2022-08-17 20:28:33 UTC  
> Url: https://github.com/boostorg/beast/issues/2503#issuecomment-1218459606  

Oddly enough I recently wrote a blog on handling HTTP redirect in beast.  
https://cppalliance.org/richard/2022/08/10/RichardsAugustUpdate.html

---

## Comment 7

> Username: ashjas  
> Created at: 2022-08-18 09:58:57 UTC  
> Url: https://github.com/boostorg/beast/issues/2503#issuecomment-1219293444  

Thanks @sehe @madmongo1 for the inputs.
