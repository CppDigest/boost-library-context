# #2298 - Handling 100-continue in Async Server [Closed]

> Username: benstadin  
> Created at: 2021-08-12 14:39:44 UTC  
> Updated at: 2021-08-12 15:36:10 UTC  
> Closed at: 2021-08-12 15:36:10 UTC  
> Url: https://github.com/boostorg/beast/issues/2298  

I'm trying to handle 100-continue in an async server. For the synchronous case I found the example here:  
  
https://www.boost.org/doc/libs/master/libs/beast/doc/html/beast/more_examples/expect_100_continue_server.html  
  
What I came up with so far ends in an endless loop:  
  
 ```  
void onRead(beast::error_code ec, std::size_t bytesTransferred) {  
// ...  
           if (_parser->get()[http::field::expect] == "100-continue")  
                {  
                    _buffer.consume(bytesTransferred);  
                      
                    // send 100 response  
                    http::response<http::empty_body> res;  
                    res.version(11);  
                    res.result(http::status::continue_);  
                    res.set(http::field::server, "my-server");  
                    _queue(std::move(res));  
                      
                    http::async_read(derived().stream(),  
                                     _buffer,  
                                     *_parser,  
                                     beast::bind_front_handler(&HttpSession::onRead,  
                                                               derived().shared_from_this()));                      
                    return;  
                }  
}  
```  
  
My understanding for handling "100-continue" is this:  
1. respond with http::status::continue  
2. read next message  
  
Is that wrong? Could you maybe give a short example, based on the http_server_flex.cpp demo?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2021-08-12 14:44:11 UTC  
> Updated at: 2021-08-12 14:59:44 UTC  
> Url: https://github.com/boostorg/beast/issues/2298#issuecomment-897700115  

This is how continue works:  
  
1. Client sends the HTTP header for a request (not the body), with "Expect: 100-continue"  
2. Server reads the request header ONLY (not the body), and sees "Expect: 100-continue"  
3. Server performs a calculation to determine if the request is valid  
4. If the request is invalid, server responds immediately with a response containing the status code  
5. Otherwise, the server sends a response with a 100: Continue   
6. If the client sees a 100 status response, it proceeds with sending the body.  
  
To make all this work you have to use functions like `read_header` or `async_read_header` in order to read just the header. The purpose of Expect / Continue is to allow the client to avoid tying up resources sending a large body if the server is going to reject it anyway.   
  
Also, the HTTP header field values are typically case-insensitive, you can use `beast::iequals` to perform the comparison (which skips all that annoying `std::locale` junk).

---

## Comment 2

> Username: vinniefalco  
> Created at: 2021-08-12 14:52:40 UTC  
> Url: https://github.com/boostorg/beast/issues/2298#issuecomment-897706777  

I'll note that the example you linked, does in fact read the header first followed by the body :)

---

## Comment 3

> Username: benstadin  
> Created at: 2021-08-12 15:15:45 UTC  
> Url: https://github.com/boostorg/beast/issues/2298#issuecomment-897725543  

Thanks for the quick response. Hm, looks like I'd have to make a few invasive changes to my code base. I'm wondering whether it is worth it at all. I saw 100-continue the first time today when a PUT request took suspiciously close to a second when using curl when the body size was > 1kb.   
  
I see different opinions about 100-continue. Should a REST API implement it as good practice? Other than checking for the file size and thereby eventually rejecting a request early, the reason for rejecting a request is in my case usually due to invalid data in the body.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2021-08-12 15:27:59 UTC  
> Url: https://github.com/boostorg/beast/issues/2298#issuecomment-897735113  

If your bodies are small, you don't need it. But if you want a client to know whether their 2GB upload is going to be rejected before they send the data, well.... :)

---

## Comment 5

> Username: benstadin  
> Created at: 2021-08-12 15:36:10 UTC  
> Url: https://github.com/boostorg/beast/issues/2298#issuecomment-897741245  

Thanks. That won't be the case for my application.
