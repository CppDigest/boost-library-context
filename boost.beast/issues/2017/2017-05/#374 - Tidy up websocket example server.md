# #374 - Tidy up websocket example server [Closed]

> Username: vinniefalco  
> Created at: 2017-05-22 15:33:19 UTC  
> Updated at: 2017-06-10 06:11:50 UTC  
> Closed at: 2017-06-10 06:11:50 UTC  
> Url: https://github.com/boostorg/beast/issues/374  

We can probably do away with that weird options_set. The example should be as lean as possible since people are likely to copy and paste it.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-05-22 15:34:43 UTC  
> Updated at: 2017-05-22 15:34:49 UTC  
> Url: https://github.com/boostorg/beast/issues/374#issuecomment-303136174  

If std::function doesnt work because of msvc we can just make a virtual function to set options on a new websocket stream and let users subclass the server in order to override it.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-05-22 15:45:51 UTC  
> Url: https://github.com/boostorg/beast/issues/374#issuecomment-303139255  

The destructor should move `ec` to the lambda for clarity  
https://github.com/vinniefalco/Beast/blob/master/examples/websocket_async_echo_server.hpp#L150  
```  
        ios_.dispatch(  
            [&]  
            {  
                error_code ec;  
                acceptor_.close(ec);  
            });  
```
