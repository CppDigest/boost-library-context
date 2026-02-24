# #2109 - Potential memory leak in WebSocket Server Chat-Multi example [Closed]

> Username: ghost  
> Created at: 2020-10-21 05:35:17 UTC  
> Updated at: 2020-10-22 10:42:06 UTC  
> Closed at: 2020-10-22 10:42:06 UTC  
> Url: https://github.com/boostorg/beast/issues/2109  

I wanted to clarify whether the following code from the current official WebSocket Server Chat-Multi example program is a potential memory leak or not.  
  
File: beast/example/websocket/server/chat-multi/websocket_session.hpp  
  
```c++  
template<class Body, class Allocator>  
void  
websocket_session::  
run(http::request<Body, http::basic_fields<Allocator>> req)  
{  
    // Set suggested timeout settings for the websocket  
    ws_.set_option(  
        websocket::stream_base::timeout::suggested(  
            beast::role_type::server));  
  
    // Set a decorator to change the Server of the handshake  
    ws_.set_option(websocket::stream_base::decorator(  
        [](websocket::response_type& res)  
        {  
            res.set(http::field::server,  
                std::string(BOOST_BEAST_VERSION_STRING) +  
                    " websocket-chat-multi");  
        }));  
  
    // Accept the websocket handshake  
    ws_.async_accept(  
        req,  
        beast::bind_front_handler(  
            &websocket_session::on_accept,  
            shared_from_this()));  
}  
```  
The http::request is passed to the run function by value, and then to the async_accept function by reference.  The official boost doc for async_accept states that  "Ownership is not transferred" with respects to the request object. Without diving into the implementation code, it seems as though the http::request object will be destroyed when the run function returns, and likely before the async_accept operation completes.  
  
Docs: https://www.boost.org/doc/libs/1_74_0/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/async_accept/overload3.html  
  
I am sure that the example code is well tested and that there is no memory leak here, but I can't be sure from the example code and documentation alone, can someone please clarify this for me?

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-10-21 06:31:57 UTC  
> Url: https://github.com/boostorg/beast/issues/2109#issuecomment-713341820  

Looking into it

---

## Comment 2

> Username: madmongo1  
> Created at: 2020-10-21 06:49:24 UTC  
> Updated at: 2020-10-21 06:50:01 UTC  
> Url: https://github.com/boostorg/beast/issues/2109#issuecomment-713349782  

@m1op1x   
  
Thanks for the issue.  
  
On the face of it it's a good catch, but looking deeper...  
  
The documentation says:  
  
> the implementation will not access this object from other threads.  
  
It probably does not say enough about what is done with this reference, but if you dig deep into the async operation here:  
  
https://github.com/boostorg/beast/blob/855fc23885307aeb6ad1318001a4bbdd4345d47c/include/boost/beast/websocket/impl/accept.hpp#L192  
  
you'll see that the `req` reference is used exactly once to construct the response object (which is stored in the composed operation).  
  
If you follow the flow of code to this point you'll see that so far the entire operation has been sequential and the async operation has not yet been started.  
  
The code becomes asynchronous here https://github.com/boostorg/beast/blob/855fc23885307aeb6ad1318001a4bbdd4345d47c/include/boost/beast/websocket/impl/accept.hpp#L217  
  
at the first YIELD.  
  
Beast documentation is written along the lines of the Asio documentation and is meant to be interpreted literally. If an object must be kept alive while an asynchronous operation is running, it will be explicitly stated in the text.  
  
If it is not, you may assume that there are no requirements on the obect's lifetime.

---

## Comment 3

> Username: ghost  
> Created at: 2020-10-22 10:42:06 UTC  
> Url: https://github.com/boostorg/beast/issues/2109#issuecomment-714404981  

@madmongo1   
  
Thank you very much for the detailed response, that makes perfect sense!  
  
I had a feeling that delving into the source would have answered my question, but my intellisense would not take me to the symbol. I shall close the issue.
