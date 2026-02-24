# #2330 - websocket_server_async example with strand ws [Closed]

> Username: PhysicsX  
> Created at: 2021-10-19 17:43:31 UTC  
> Updated at: 2021-10-19 18:21:09 UTC  
> Closed at: 2021-10-19 18:21:09 UTC  
> Url: https://github.com/boostorg/beast/issues/2330  

Beast version 1.71.0  
  
Hello,  
  
I have a simple question about websocket_server_async.cpp example.  
  
In this example session does not use strand. Only listener.   
Why we do not need strand for ws ?   
If I want to use it how should I use it ?  
When construct the ws with boost::asio::make_strand(socket.get_executor()) like this   
(as I know there is no need for 1.7 version to bind with strand anymore. )  
`    session(tcp::socket&& socket)  
        : ws_(boost::asio::make_strand(socket.get_executor()))  
`  
then it compiles but it gives Bad file descriptor error during ws_.async_accept.  
`accept: Bad file descriptor`  
  
Could you please help me about this issue ?  How can I use listener websocket with strand ?  
  
Thank you.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2021-10-19 17:49:27 UTC  
> Updated at: 2021-10-19 17:49:36 UTC  
> Url: https://github.com/boostorg/beast/issues/2330#issuecomment-946959241  

> Why we do not need strand for ws ?  
  
The latest version of the example is multithreaded, and uses strands:  
https://github.com/boostorg/beast/blob/b15a5ff0e47e72ba3d4711d2514bc65749d036ae/example/websocket/server/async/websocket_server_async.cpp#L222  
  
Consider using Boost 1.77 or later?

---

## Comment 2

> Username: PhysicsX  
> Created at: 2021-10-19 18:10:24 UTC  
> Url: https://github.com/boostorg/beast/issues/2330#issuecomment-946976017  

I can use 1.77 boost or later no problem.  
  
Okay for listener is enough to use it ? Why we do not need it for session class ? As I remember in the old versions for session and listener are using the strand. ( if I am correctly remember ).

---

## Comment 3

> Username: vinniefalco  
> Created at: 2021-10-19 18:17:31 UTC  
> Url: https://github.com/boostorg/beast/issues/2330#issuecomment-946981353  

> Okay for listener is enough to use it ? Why we do not need it for session class ?  
  
We do need it in the session. This statement:  
```  
        acceptor_.async_accept(  
            net::make_strand(ioc_),  
            beast::bind_front_handler(  
                &listener::on_accept,  
                shared_from_this()));  
```  
  
assigns a new strand to the new socket for every incoming connection. The strand is transferred to the session when the socket is moved:  
  
https://github.com/boostorg/beast/blob/b15a5ff0e47e72ba3d4711d2514bc65749d036ae/example/websocket/server/async/websocket_server_async.cpp#L238

---

## Comment 4

> Username: PhysicsX  
> Created at: 2021-10-19 18:21:09 UTC  
> Url: https://github.com/boostorg/beast/issues/2330#issuecomment-946984301  

Thank you
