# #2349 - Set time_out for http_client async operations [Closed]

> Username: zhouxindong  
> Created at: 2021-11-23 08:06:50 UTC  
> Updated at: 2021-11-23 09:13:51 UTC  
> Closed at: 2021-11-23 09:13:51 UTC  
> Url: https://github.com/boostorg/beast/issues/2349  

Hi, I use the beast http demo at https://www.boost.org/doc/libs/1_77_0/libs/beast/example/http/client/async-ssl/http_client_async_ssl.cpp  
  
I found  
 `beast::get_lowest_layer(stream_).expires_after(std::chrono::seconds(30));`   
called more than once.  
  
My question is why some async operations need set time out before and others not need.   
I remove below code from on_handshake:  
  
`beast::get_lowest_layer(stream_).expires_after(std::chrono::seconds(30));`   
  
and run it, even `async_read` call successfully,   
`        if(ec)  
            return fail(ec, "read");`   
  
I received output: `read: The socket was closed due to a timeout.`  
Thanks a lot.
