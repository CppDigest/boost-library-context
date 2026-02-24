# #2479 - Teardown javadocs need improvement [Closed]

> Username: vinniefalco  
> Created at: 2022-07-03 16:23:24 UTC  
> Updated at: 2024-01-26 09:27:36 UTC  
> Closed at: 2024-01-26 09:27:36 UTC  
> Url: https://github.com/boostorg/beast/issues/2479  

The docs for `teardown` and `async_teardown` need to make it clear that they are customization points and should not be called directly. See: https://www.boost.org/doc/libs/1_79_0/libs/beast/doc/html/beast/using_websocket/teardown.html

---

## Comment 1

> Username: alandefreitas  
> Created at: 2022-07-04 15:02:42 UTC  
> Url: https://github.com/boostorg/beast/issues/2479#issuecomment-1173916866  

I'm replicating the discussion here for reference.  
  
Question:  
  
> Hello gents, I am trying to teardown a websocket stream on the server side, but the compiler complains that no matching function can be found. What am I missing?  
```cpp  
websocket::stream<tcp::socket> ws_;  
        beast::async_teardown(beast::role_type::server, ws_.next_layer(),  
                        [id=id_, on_close=std::move(on_close)](beast::error_code const& ec)  
                        {  
                          on_close(id, ec);  
                        });  
```  
  
Answer:  
  
> That function is not meant to be called directly  
> It is a customization point  
> Just call `websocket::stream::async_close`  
> The [docs](https://www.boost.org/doc/libs/1_79_0/libs/beast/doc/html/beast/ref/boost__beast__websocket__async_teardown/overload1.html) are terrible  
> But it is better explained [here](https://www.boost.org/doc/libs/1_79_0/libs/beast/doc/html/beast/using_websocket/teardown.html)  
  
  
> I see, thanks. Yeah, I read both pages, but came away thinking that I could still call it as long as I provided the underlying socket properly. The thing that lead me down this route is trying to deal with a specific case. Server gets a message and tries to send it over to its final destination - it manages to write the contents using async_write at which point the stream gets closed with async_close - what happens to the written bytes? Are they guaranteed to be sent or will close prevent that? Teardown appeared to do the right thing, although the wording is still a little ambiguous: This operation cleanly discards bytes remaining at receiving endpoints and also closes the underlying TCP/IP connection. If close properly sends the bytes over I am all set, but if not - is there a way for me to guarantee the send?  
  
> if you wait for async_write to complete before calling async_close, then the message is guaranteed to be written  
> The comment refers to data received before calling async_close which you did not yet acquire with async_read

---

## Comment 2

> Username: ashtum  
> Created at: 2024-01-26 09:27:36 UTC  
> Url: https://github.com/boostorg/beast/issues/2479#issuecomment-1911736919  

Addressed in https://github.com/boostorg/beast/pull/2808.
