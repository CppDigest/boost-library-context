# #827 - Byte chunk in HTTP POST request [Closed]

> Username: EdvardD  
> Created at: 2017-10-23 19:21:13 UTC  
> Updated at: 2017-10-24 13:01:14 UTC  
> Closed at: 2017-10-24 13:01:14 UTC  
> Url: https://github.com/boostorg/beast/issues/827  

I wanna send bytes in body of HTTP POST request.  
I've tried:  
~~~http::request<http::buffer_body> req{http::verb::post, target, 11};  
req.set(http::field::host, host);  
req.set(http::field::content_type, "application/octet-stream");  
char s[] = "hello";  
req.body().data = s;  
req.body().size = sizeof(s);  
req.body().more = false;  
~~~  
  
and:  
  
~~~  
http::request<http::string_body> req{http::verb::post, target, 11};  
req.set(http::field::host, host);  
req.set(http::field::content_type, "application/octet-stream");  
req.body().append("hello");  
~~~  
But in both cases server got an empty body.  
What I'm doing wrong?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-10-23 19:32:08 UTC  
> Url: https://github.com/boostorg/beast/issues/827#issuecomment-338771257  

HTTP requests with a payload **must** specify either the Content-Length or use the chunked Transfer-Encoding. Based on your code, you can do this two ways:  
```  
req.content_length(sizeof(s));  
```  
or  
```  
req.prepare_payload();  
```

---

## Comment 2

> Username: EdvardD  
> Created at: 2017-10-24 13:00:19 UTC  
> Url: https://github.com/boostorg/beast/issues/827#issuecomment-338981524  

Thanks it works
