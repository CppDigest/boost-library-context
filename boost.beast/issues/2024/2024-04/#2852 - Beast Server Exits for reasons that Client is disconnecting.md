# #2852 - Beast Server Exits for reasons that Client is disconnecting? [Closed]

> Username: vtharmalingam  
> Created at: 2024-04-06 15:38:52 UTC  
> Updated at: 2024-04-09 05:24:17 UTC  
> Closed at: 2024-04-09 05:24:17 UTC  
> Url: https://github.com/boostorg/beast/issues/2852  

### Version of Beast  
1.84  
  
I hope this finds you well. I've been utilizing the Beast lib successfully for over three years! Great library!  
  
It's not a frequent occurrence, but occasionally, I encounter this error where the beast server exits with the following message. I'm curious about the possible cause and whether there's anything specific I should address, perhaps within the fail method or elsewhere? It cause some sort of stability issue. Thank you, as always!  
  
```  
write: An existing connection was forcibly closed by the remote host  
read: The I/O operation has been aborted because of either a thread exit or an application request  
read: An existing connection was forcibly closed by the remote host  
```  
  
Additionally, I have a question on the side:  
How can I retrieve the client's IP address based on beast::ssl_stream<beast::tcp_stream> stream?  
  
Thanks,  
Tharma

---

## Comment 1

> Username: ashtum  
> Created at: 2024-04-06 18:04:15 UTC  
> Url: https://github.com/boostorg/beast/issues/2852#issuecomment-2041153139  

> It's not a frequent occurrence, but occasionally, I encounter this error where the beast server exits with the following message. I'm curious about the possible cause and whether there's anything specific I should address  
  
It appears that a client connection is being closed ungracefully based on the error messages. I'm not sure about your error handling approach, but typically, your server application shouldn't terminate due to client connection errors. It's important to anticipate that asynchronous operations on client sockets may encounter failures.  
  
> How can I retrieve the client's IP address based on beast::ssl_streambeast::tcp_stream stream?  
  
```C++  
beast::tcp_stream tcp_stream{ ioc };  
beast::ssl_stream<beast::tcp_stream> ssl_stream{ ioc, ssl_ctx };  
  
auto ep1 = get_lowest_layer(ssl_stream).socket().remote_endpoint();  
auto ep2 = tcp_stream.socket().remote_endpoint();  
```

---

## Comment 2

> Username: vtharmalingam  
> Created at: 2024-04-07 07:59:35 UTC  
> Url: https://github.com/boostorg/beast/issues/2852#issuecomment-2041357933  

Thank you very much.
