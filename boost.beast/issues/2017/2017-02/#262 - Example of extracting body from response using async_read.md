# #262 - Example of extracting body from response using async_read [Closed]

> Username: clemahieu  
> Created at: 2017-02-10 16:18:41 UTC  
> Updated at: 2017-02-10 16:25:28 UTC  
> Closed at: 2017-02-10 16:24:05 UTC  
> Url: https://github.com/boostorg/beast/issues/262  

I'm trying to use the http_example.cpp but modifying it to do async_read and I haven't found a way to extract just the body in to a string.  This is my setup:  
  
```  
boost::asio::ip::tcp::socket sock;  
beast::streambuf sb;  
beast::http::response<beast::http::streambuf_body> resp;  
beast::http::async_read(sock, sb, resp, [this] (boost::system::error_code & ec)  
{  
  // Get body only?  
  std::cerr << resp;  
});  
```  
  
All of resp is printed but I just want the body.  resp.body doesn't seem to support operator<< in to a stream.  I tried changing streambuf/streambuf_body for std::string/string_body but that doesn't seem to work for async_read.  I can iterate over sb but I can't find a way to extract any of the information.  
  
Is there a simple way to get the body as a string after async_read has completed?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-02-10 16:20:19 UTC  
> Url: https://github.com/boostorg/beast/issues/262#issuecomment-278988692  

Finally, an easy question :) Just change your declaration:  
```  
beast::http::response<beast::http::string_body> resp;  
```  
  
See:  
http://vinniefalco.github.io/beast/beast/http/body.html

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-02-10 16:22:29 UTC  
> Url: https://github.com/boostorg/beast/issues/262#issuecomment-278989269  

You're right that the `streambuf` is a sequence of buffers. If you want to keep the `streambuf_body` there's also this function  
http://vinniefalco.github.io/beast/beast/ref/to_string.html  
  
You could change your original code to:  
```  
boost::asio::ip::tcp::socket sock;  
beast::streambuf sb;  
beast::http::response<beast::http::streambuf_body> resp;  
beast::http::async_read(sock, sb, resp, [this] (boost::system::error_code & ec)  
  {  
    std::cerr << beast::to_string(resp.data());  
  });  
```

---

## Comment 3

> Username: clemahieu  
> Created at: 2017-02-10 16:24:05 UTC  
> Url: https://github.com/boostorg/beast/issues/262#issuecomment-278989704  

Worked perfect, I spent an embarrassing amount of time trying to figure out how to do that.  I don't know if there's a better way to document it, I might just be slow ;)  
  
Thanks again!
