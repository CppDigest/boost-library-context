# #2160 - Keep the connection open by holding tcp_stream, how can I know tcp_stream is closed by server? [Closed]

> Username: xiewei20082008  
> Created at: 2021-02-09 12:48:50 UTC  
> Updated at: 2021-02-10 17:23:25 UTC  
> Closed at: 2021-02-10 17:23:25 UTC  
> Url: https://github.com/boostorg/beast/issues/2160  

When the server's keep_alive() is true, as an HTTP client, I use tcp_stream as a class member to keep the connection open, but how can I know if the tcp_stream has been closed by the server? Am I only able to know it until I send another request?  
  
 is there anything like on_close method for tcp_stream to allow me to register a callback?  
  
Thank you in advance!

---

## Comment 1

> Username: madmongo1  
> Created at: 2021-02-09 13:01:32 UTC  
> Url: https://github.com/boostorg/beast/issues/2160#issuecomment-775919108  

Tcp connections are blind. You can’t know anything about what the remote peer has done until you receive (or don’t receive) a packet.  
  
you test the remote connection by using it. On first failure, you open a new connection.  
  
I blogged about this and offered an example implementation in my new year blog.  
  
https://cppalliance.org/richard/2021/01/01/RichardsNewYearUpdate.html

---

## Comment 2

> Username: xiewei20082008  
> Created at: 2021-02-10 17:23:25 UTC  
> Url: https://github.com/boostorg/beast/issues/2160#issuecomment-776877387  

Thank you for your reply. Helped me a lot!
