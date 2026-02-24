# #1775 - When async completing websocket handshake with request object, does DynamicBuffer still need to be around? [Closed]

> Username: jselbie  
> Created at: 2019-12-01 00:49:08 UTC  
> Updated at: 2019-12-01 03:09:53 UTC  
> Closed at: 2019-12-01 03:09:53 UTC  
> Url: https://github.com/boostorg/beast/issues/1775  

TLDR: Does the `DynamicBuffer` instance that was referenced alongside a `request` object int `http::async_read` have to also live for the lifetime of the websocket upgrade handshake?  
  
Assume everything is asynchronous.  
  
Kicking off an http request read:  
  
```  
http::async_read(_sock, _flat_buffer, _req, [spThis = shared_from_this()](const boost::system::error_code& ec, std::size_t bytes_transferred) {  
           if (!ec && websocket::is_upgrade(_req))  
           {  
                 ...  
           }  
    }  
```  
  
Both `_flat_buffer` and `_req` are instances of `flat_buffer` and `request` that live as member of "this" class instance.  
  
What's not clear to me at this point, when the handler is invoked if the flat_buffer instance is still the the backing memory for the `request` object that was also passed to `async_read`.  
  
I ask, because if I immediately kick off this code within the handler.  
  
    websocket::stream<tcp::socket> ws(std::move(spThis->_sock));  
    ws.async_accept(spThis->_req, [] (){  
         /*handler code*/  
   });  
  
Do I need to insure the lifetime of `_flat_buffer` also persists for the duration of the `async_accept` call until the accept handler is finally called.  
  
And while I'm asking, I'm assuming the `request` object itself has to persist until the accept handler is called as well.  Correct?  
  
Why I ask - When the http socket is upgraded to websocket, I want to actually have the my web socket code run in a different class separate from the code managing http sessions.  I need to know what objects aside from the socket need to be moved over to the new class instance.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-12-01 00:51:31 UTC  
> Url: https://github.com/boostorg/beast/issues/1775#issuecomment-560034640  

> Do I need to insure the lifetime of flat_buffer also persists for the duration of the async_accept call until the accept handler is finally called.  
  
No.  
  
> I'm assuming the request object itself has to persist until the accept handler is called as well. Correct?  
  
Yes. See: https://www.boost.org/doc/libs/1_71_0/doc/html/boost_asio/reference/asynchronous_operations.html#boost_asio.reference.asynchronous_operations.lifetime_of_initiating_function_arguments

---

## Comment 2

> Username: jselbie  
> Created at: 2019-12-01 03:09:53 UTC  
> Url: https://github.com/boostorg/beast/issues/1775#issuecomment-560046026  

Got it. Thank you for the fast reply.
