# #2486 - Is there any portable way to know if the socket has timed out on client side vs on server side? [Closed]

> Username: ashjas  
> Created at: 2022-07-20 14:37:20 UTC  
> Updated at: 2022-09-24 15:36:36 UTC  
> Closed at: 2022-09-24 15:36:36 UTC  
> Url: https://github.com/boostorg/beast/issues/2486  

Beast version 1.71  
  
Hi,   
Is there any portable way to know if the socket has timed out on client side vs on server side?  
As far as i have tested, on linux, i get error code **1** for both client side timeout and server side socket close [not able to emulate serverside socket timeout so instead closed the socket]  
On windows, i get error code **1** for client side timeout and **10053** for server side socket close.  
  
So how do i definitely know on linux if its a client vs server socket timeout?

---

## Comment 1

> Username: sehe  
> Created at: 2022-07-20 16:18:09 UTC  
> Url: https://github.com/boostorg/beast/issues/2486#issuecomment-1190486042  

Who is claiming the timeout?  
  
A client timeout is when the client abandons an async operation due exceeding a time limit.  
  
A server timeout is when the server does the same.  
  
Each party *always* knows when they are cancelling an operation. If the "other end" closes the connection, there will not be a timeout, instead it will look like the connection is closed (connection reset, short read or eof).  
  
In short, I think you need to show some code to demonstrate how you're getting the values you describe.

---

## Comment 2

> Username: ashjas  
> Created at: 2022-07-21 02:16:03 UTC  
> Updated at: 2022-07-21 02:18:03 UTC  
> Url: https://github.com/boostorg/beast/issues/2486#issuecomment-1190959528  

at client side timeout is set as:  
```m_stream.expires_after(std::chrono::seconds(secs));```  
at server end, it may have its own policy when to timeout sockets, so as to save resources. In our usage its after 300secs of idle socket.(we dont have control on server's policy , it can be anything)  
In both the cases i get the above error_codes as mentioned on linux and windows plats.  
On linux in both cases i get 1 as error code.  
Depending on this difference, i need to take action like, if client soc timesout, i dont need to do X while do Y if server's end of sock timedout.  
So how can i differentiate between the two on linux?

---

## Comment 3

> Username: sehe  
> Created at: 2022-07-21 09:44:29 UTC  
> Url: https://github.com/boostorg/beast/issues/2486#issuecomment-1191274966  

> On linux in both cases i get 1 as error code.  
  
That doesn't really make sense to me. Again, I think instead of describing things, we need a minimal example that shows what you're doing. (E.g. you just accidentally told use that you're using one of Beast's stream models, because you added that you're using `m_stream.expires_after`, but we still don't know what model. Actually, we don't even know that you're using async operations, or how the service is run)

---

## Comment 4

> Username: klemens-morgenstern  
> Created at: 2022-09-24 04:24:52 UTC  
> Url: https://github.com/boostorg/beast/issues/2486#issuecomment-1256852720  

@ashjas has your question been answered?

---

## Comment 5

> Username: ashjas  
> Created at: 2022-09-24 15:36:36 UTC  
> Url: https://github.com/boostorg/beast/issues/2486#issuecomment-1256995941  

@klemens-morgenstern yes
