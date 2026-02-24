# #767 - The HTTP sync client example fails if using HTTP 1.0 [Closed]

> Username: sorf  
> Created at: 2017-09-07 07:46:23 UTC  
> Updated at: 2017-10-25 16:13:53 UTC  
> Closed at: 2017-10-22 22:24:30 UTC  
> Url: https://github.com/boostorg/beast/issues/767  

The http_client_sync.cpp example fails with:   
```  
Error: end of stream  
```  
if using HTTP 1.0 instead of 1.1   
  
### Version of Beast  
112  
  
### Steps necessary to reproduce the problem  
The code change to expose the problem:  
https://github.com/sorf/cpp-libraries-tests/commit/5271a80e7812a2310dd2fa8c2823b08f70636ab8#diff-ec9843e2cca371ed43d59757896fd2a3  
  
### All relevant compiler information  
  
It happens on both Windows (Visual Studio 17 and MinGW gcc 7.1) and on Linux (gcc 7 and clang 3.8).  
The test program is run with the suggested parameters:  
```  
www.example.com 80 /  
```  
  
curl shows that www.example.com accepts both HTTP 1.1 and 1.0:  
  
```  
>curl -v --http1.0 -o /dev/null www.example.com  
<snip>  
* Connected to www.example.com (93.184.216.34) port 80 (#0)  
> GET / HTTP/1.0  
> User-Agent: curl/7.37.0  
> Host: www.example.com  
> Accept: */*  
>  
* HTTP 1.0, assume close after body  
< HTTP/1.0 200 OK  
<snip>  
< Content-Length: 1270  
< Connection: close  
<snip>  
```  
```  
>curl -v --http1.1 -o /dev/null www.example.com  
<snip>  
* Connected to www.example.com (93.184.216.34) port 80 (#0)  
> GET / HTTP/1.1  
> User-Agent: curl/7.37.0  
> Host: www.example.com  
> Accept: */*  
>  
< HTTP/1.1 200 OK  
<snip>  
< Content-Length: 1270  
<snip>  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-09-07 14:04:27 UTC  
> Updated at: 2017-09-07 14:04:47 UTC  
> Url: https://github.com/boostorg/beast/issues/767#issuecomment-327809578  

This is working as designed. `http::write` (and `http::async_write`) will return `http::error::end_of_stream` when the message being sent indicates that the connection should be closed. You need to modify the example thusly:  
```  
try {  
    http::write(socket, req);  
}  
catch(beast::system_error const& se) {  
    if(se.code() != http::error::stream_closed)  
        throw;  
    socket.shutdown(tcp::socket::shutdown_send);  
}  
```  
  
See:  
http://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/ref/boost__beast__http__error.html

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-09-07 14:10:55 UTC  
> Url: https://github.com/boostorg/beast/issues/767#issuecomment-327811415  

You could argue that perhaps we should change this behavior. For example, to only happen when writing a response.

---

## Comment 3

> Username: sorf  
> Created at: 2017-09-08 14:23:20 UTC  
> Url: https://github.com/boostorg/beast/issues/767#issuecomment-328116904  

I think it's fine the way it is even for the request side, now that I know of it.  
Maybe a change similar to: https://github.com/sorf/cpp-libraries-tests/commit/0f18c59a002043b7830c66afac6c030697186571 can make its way in the example?

---

## Comment 4

> Username: vinniefalco  
> Created at: 2017-09-08 17:15:44 UTC  
> Url: https://github.com/boostorg/beast/issues/767#issuecomment-328162773  

Not a bad idea!

---

## Comment 5

> Username: vinniefalco  
> Created at: 2017-09-08 17:16:11 UTC  
> Url: https://github.com/boostorg/beast/issues/767#issuecomment-328162857  

The client examples can be modified to allow choosing the HTTP version.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2017-10-22 14:02:42 UTC  
> Url: https://github.com/boostorg/beast/issues/767#issuecomment-338479878  

>I think it's fine the way it is even for the request side, now that I know of it.  
  
Hmm.. no, I don't like it. Because it is confusing to return an error even when the operation is successful. And when using exceptions it breaks the flow of control for nothing. I have made the change, it is in version 124. I like your change with the HTTP version but it would need to be applied to all the clients, can you submit a pull request against 124?

---

## Comment 7

> Username: sorf  
> Created at: 2017-10-25 16:13:52 UTC  
> Url: https://github.com/boostorg/beast/issues/767#issuecomment-339384538  

> I like your change with the HTTP version but it would need to be applied to all the clients, can you submit a pull request against 124?  
  
Please see pull request #833.
