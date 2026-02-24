# #2497 - Best practices for extracting values out of beast::flat_buffer [Closed]

> Username: dpsingh28  
> Created at: 2022-08-10 22:39:14 UTC  
> Updated at: 2022-08-16 07:58:39 UTC  
> Closed at: 2022-08-16 07:58:39 UTC  
> Url: https://github.com/boostorg/beast/issues/2497  

Hi I am working on an API for getting radio status variables, using the best websocket library. I am following this example: [https://www.boost.org/doc/libs/master/libs/beast/example/websocket/client/sync-ssl/websocket_client_sync_ssl.cpp](https://www.boost.org/doc/libs/master/libs/beast/example/websocket/client/sync-ssl/websocket_client_sync_ssl.cpp).  
  
I wanted to know, what are the best practices to extract values from flat_buffers. I have tried out the following things:  
- Conversion to boost json object  
- Conversion to nholmann json object  
  
In both the cases, I end up with some compatibility issues, leading to a core dump. The current working solution that I have is to use regex or general string matching to extract the values, which seems a bit inefficient. Is there some other efficient way to query the variable values in the flat_buffer response message?

---

## Comment 1

> Username: mclow  
> Created at: 2022-08-10 22:57:24 UTC  
> Url: https://github.com/boostorg/beast/issues/2497#issuecomment-1211368340  

This is probably best asked on the boost-users mailing list (boost-users@lists.boost.org)

---

## Comment 2

> Username: madmongo1  
> Created at: 2022-08-11 06:01:42 UTC  
> Url: https://github.com/boostorg/beast/issues/2497#issuecomment-1211581279  

Can you show the code you’ve tried? This should be trivial.  
A core dump is not a “compatibility issue”, it’s the result of a logic error.
