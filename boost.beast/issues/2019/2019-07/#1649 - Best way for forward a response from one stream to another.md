# #1649 - Best way for forward a response from one stream to another? [Closed]

> Username: inetic  
> Created at: 2019-07-02 14:09:54 UTC  
> Updated at: 2019-07-03 09:03:34 UTC  
> Closed at: 2019-07-03 09:03:34 UTC  
> Url: https://github.com/boostorg/beast/issues/1649  

Hello,  
  
I'm looking for a way to "forward" an HTTP response from one stream to another while also being able to read the header and intercept the response based on some fields in that header. Basically something along these lines, but without the need to store the entire response body in the memory before it's forwarded:  
  
```  
void forward(tcp::socket source, tcp::socket sink, asio::yield_context yield) {  
    beast::flat_buffer buffer;  
    http::response<http::dynamic_body> rs;  
    beast::http::async_read(source, buffer, rs, yield);  
    if (!my_is_good_response(rs.base()) {  
        my_send_bad_response(sink, yield);  
        return;  
    }  
    beast::http::async_write(sink, rs, yield);  
}  
```

---

## Comment 1

> Username: cmazakas  
> Created at: 2019-07-02 15:10:10 UTC  
> Updated at: 2019-07-02 15:10:27 UTC  
> Url: https://github.com/boostorg/beast/issues/1649#issuecomment-507720012  

Howdy, inetic  
  
I've written something similar for an HTTP proxy server.  
  
To accomplish your goal, it's advisable to follow the example here:  
https://www.boost.org/doc/libs/1_70_0/libs/beast/doc/html/beast/more_examples/http_relay.html  
  
The idea here is that you use Beast to read in the headers first, you can process them however you wish and then you can start writing back however you want.  
  
The example in Beast doesn't complete the entire picture for HTTP as HTTP proxies are required by the RFC to remove/add certain header fields.  
  
For a somewhat more complete example, here's my relay and its tests:  
Impl: https://github.com/LeonineKing1199/foxy/blob/develop/include/foxy/detail/relay.hpp  
Test: https://github.com/LeonineKing1199/foxy/blob/develop/test/relay_test.cpp  
  
Let me know if you have any more questions or need clarification. The Beast `buffer_body` is a very fun type to play around with!

---

## Comment 2

> Username: inetic  
> Created at: 2019-07-03 06:45:49 UTC  
> Url: https://github.com/boostorg/beast/issues/1649#issuecomment-507963085  

Those are all very nice examples. Thank you
