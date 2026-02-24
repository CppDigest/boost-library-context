# #2934 - async_write_header error [Closed]

> Username: xiaoma565  
> Created at: 2024-09-24 04:14:28 UTC  
> Updated at: 2024-09-25 07:15:39 UTC  
> Closed at: 2024-09-25 07:15:39 UTC  
> Url: https://github.com/boostorg/beast/issues/2934  

I made a demo of http server. When the server received a request, it will send 10 chunk messages.  
  
When I called write_header, it is success.  
```c++  
void send_chunked()  
{  
    http::response<http::empty_body> res{http::status::ok, req_.version()};  
    res.set(http::field::server, "Beast");  
    res.chunked(true);  
      
    // --------------------------------------------------------------------------------------  
    // call write_header  
    http::response_serializer<http::empty_body> sr{res};  
    http::write_header(stream_, sr);  
    send_chunked_body();  
    // --------------------------------------------------------------------------------------  
    // call async_write_header  
    // serializer.emplace(res);  
    // http::async_write_header(stream_, *serializer,  
    //     beast::bind_front_handler(&session::on_write_header, shared_from_this()));  
}  
```   
![image](https://github.com/user-attachments/assets/61d1cad3-4072-4614-a668-ca8b750458e8)  
  
When I called async_write_header, I got a error.  
```c++  
void send_chunked()  
{  
    http::response<http::empty_body> res{http::status::ok, req_.version()};  
    res.set(http::field::server, "Beast");  
    res.chunked(true);  
      
    // --------------------------------------------------------------------------------------  
    // call write_header  
    // http::response_serializer<http::empty_body> sr{res};  
    // http::write_header(stream_, sr);  
    // send_chunked_body();  
    // --------------------------------------------------------------------------------------  
    // call async_write_header  
    serializer.emplace(res);  
    http::async_write_header(stream_, *serializer,  
        beast::bind_front_handler(&session::on_write_header, shared_from_this()));  
}  
```   
![image](https://github.com/user-attachments/assets/f8006297-2e2b-4423-adc4-d765377f3169)  
  
[chunked_server_1.zip](https://github.com/user-attachments/files/17107840/chunked_server_1.zip)

---

## Comment 1

> Username: ashtum  
> Created at: 2024-09-24 07:36:03 UTC  
> Url: https://github.com/boostorg/beast/issues/2934#issuecomment-2370428484  

The serializer doesn't copy the response; it takes a reference to it, and it is the caller's responsibility to keep it alive (and at the same memory address) until serialization is complete. You need to make `resp` a member variable.  
[http::serializer::serializer (3 of 3 overloads)](https://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/ref/boost__beast__http__serializer/serializer/overload3.html)
