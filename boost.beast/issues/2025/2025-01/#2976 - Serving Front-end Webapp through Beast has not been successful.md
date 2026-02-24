# #2976 - Serving Front-end Webapp through Beast has not been successful [Closed]

> Username: vtharmalingam  
> Created at: 2025-01-23 18:36:09 UTC  
> Updated at: 2025-04-15 12:58:30 UTC  
> Closed at: 2025-04-15 12:58:30 UTC  
> Url: https://github.com/boostorg/beast/issues/2976  

I have a Beast WebSocket Server running over SSL, with the `server.crt`, `server.key`, and `dh2048.pem` files properly loaded. I've also placed my React front-end app in a subdirectory and served it via HTTPS.   
  
On my local machine and **even from some remote machines**, I was able to get the front-end loading successfully, and that connects to WSS.  
  
However, on other remote machines, the front-end fails to load and keeps trying indefinitely. I attempted increasing the HTTPS socket timeout and other tweaks, but nothing helped.  
  
As a workaround, I had to serve the front-end using Apache on the remote machine, and that establishes the WSS connection to the beast server.   
  
This setup introduces unnecessary complexity—requiring two SSL certificates and increasing maintenance overhead.  
  
What could I be missing? Why doesn’t the front-end consistently load across all remote machines?  
  
Any advice would be greatly appreciated! If needed, I can share the relevant code for review if that will help. I believe this could be a common issue, and you may have some tips for me to fix this. Thank you so much, in advance!  
  
Regards,  
Tharma

---

## Comment 1

> Username: ashtum  
> Created at: 2025-01-24 05:47:11 UTC  
> Updated at: 2025-01-24 05:47:31 UTC  
> Url: https://github.com/boostorg/beast/issues/2976#issuecomment-2611653818  

What type of error does [ssl::stream::async_handshake](https://www.boost.org/doc/libs/develop/doc/html/boost_asio/reference/ssl__stream/async_handshake/overload1.html) return for clients that fail to connect?  
Please provide more information, such as which step the connection reaches and what errors occur.
