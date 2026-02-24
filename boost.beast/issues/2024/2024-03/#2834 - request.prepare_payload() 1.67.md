# #2834 - request.prepare_payload() 1.67 [Closed]

> Username: NickKappy  
> Created at: 2024-03-05 20:54:03 UTC  
> Updated at: 2024-03-06 14:56:59 UTC  
> Closed at: 2024-03-06 14:56:59 UTC  
> Url: https://github.com/boostorg/beast/issues/2834  

###   
Boost 1.67  
### Steps necessary to reproduce the problem  
  
When attempting to write a request to a stream, I am performing a prepare_payload();, which appears to overwrite the content_length of my http request.  
  
I'm sure this is likely due to the content_type being = "application/json" (boost 1.67 is the tool I have to use but json.hpps aren't available in my version)  
  
        http::request<http::dynamic_body> req{http::verb::post, "/endpoint/", 11};  
        req.set(http::field::host, "1.1.1.1");  
        req.set(http::field::user_agent, BOOST_BEAST_VERSION_STRING);  
        req.set(http::field::body, "{\"username\":\"admin\", \"password\":\"admin"}");  
        req.set(http::field::content_type, "application/json");  
        req.set(http::field::content_length, lengthOfString);  
        req.prepare_payload();  
          
I have to post the log-in information this way in the body the interact properly with the certain REST server I am speaking with  
  
When printed out before prepare_payload my length is correct, but content-length is incorrect afterwards which appears to be causing me to hang when speaking with the server. Any ideas?  
  
I am not able to update my version of boost due to the nature of the project

---

## Comment 1

> Username: ashtum  
> Created at: 2024-03-06 06:36:38 UTC  
> Url: https://github.com/boostorg/beast/issues/2834#issuecomment-1980179368  

When you call [`prepare_payload()`](https://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/ref/boost__beast__http__message/prepare_payload.html), the `Content-Length` and `Transfer-Encoding` field values are automatically adjusted based on the message body.  
  
For instance:  
```C++  
http::request<http::string_body> req;  
req.method(beast::http::verb::post);  
req.body() = "body-contents";  
req.prepare_payload(); // Content-Length set based on the body  
```  
  
Typically, you won't need to explicitly set `Content-Length`. However, if necessary, you can bypass calling `prepare_payload()`, and the values set in `Content-Length` and `Transfer-Encoding` will remain unchanged.

---

## Comment 2

> Username: NickKappy  
> Created at: 2024-03-06 14:33:25 UTC  
> Url: https://github.com/boostorg/beast/issues/2834#issuecomment-1981005185  

> When you call [`prepare_payload()`](https://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/ref/boost__beast__http__message/prepare_payload.html), the `Content-Length` and `Transfer-Encoding` field values are automatically adjusted based on the message body.  
>   
> For instance:  
>   
> ```c++  
> http::request<http::string_body> req;  
> req.method(beast::http::verb::post);  
> req.body() = "body-contents";  
> req.prepare_payload(); // Content-Length set based on the body  
> ```  
>   
> Typically, you won't need to explicitly set `Content-Length`. However, if necessary, you can bypass calling `prepare_payload()`, and the values set in `Content-Length` and `Transfer-Encoding` will remain unchanged.  
  
Yes - thank you!  
  
I am aware of this and we could ignore the line to set content-length even - but prepare_paylod seems to be ignoring the size of my body when I have it formatted in a json style

---

## Comment 3

> Username: ashtum  
> Created at: 2024-03-06 14:41:31 UTC  
> Url: https://github.com/boostorg/beast/issues/2834#issuecomment-1981021869  

If this is how you are setting the message body, it is incorrect:  
```C++  
req.set(http::field::body, "{\"username\":\"admin\", \"password\":\"admin"}");  
```  
You are actually setting a header field there.  
  
This is how we set body content (assuming you are using `http::request<http::string_body>`):  
```C++  
req.body() = "body-contents";  
```

---

## Comment 4

> Username: NickKappy  
> Created at: 2024-03-06 14:52:41 UTC  
> Url: https://github.com/boostorg/beast/issues/2834#issuecomment-1981045333  

HAHA okay that explains so much - that resolves my issue thank you hahaha

---

## Comment 5

> Username: NickKappy  
> Created at: 2024-03-06 14:56:59 UTC  
> Url: https://github.com/boostorg/beast/issues/2834#issuecomment-1981054445  

Issue resolved
