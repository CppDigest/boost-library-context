# #51 - More suggestions for documentation improvements [Closed]

> Username: mzimbres  
> Created at: 2023-01-21 18:33:06 UTC  
> Updated at: 2023-02-26 19:58:16 UTC  
> Closed at: 2023-02-26 19:58:16 UTC  
> Url: https://github.com/boostorg/redis/issues/51  

> Some of the reasons for the rejections are not particularly strong but there is one recurring theme which even affected me and that is this async_run and async_exec business with the operator|| on the coroutines  
>   
> it bugged me, it bugged David, it pretty much bugs everyone who sees it  
>   
> I understand why it is there but it has to be presented better  
>   
> Aside from the general problem of the documentation needing improvement, it needs to be stated clearly and from the beginning in the README and the html docs, the library's requirements on run and exec  
>   
> I will try to give an example of some exposition  
>  
> "This library implements RESP3, a string-based protocol which can multiplex any number of client requests, responses, and server pushes onto a single active socket connection to the Redis server."  
>  
> "Due to server pushes and multiplexing, there is not a 1:1 correspondence between client requests and server results."  
>   
> "The interface for the library provides the function async_runto allow the caller to run the necessary, ongoing asynchronous operation which reads and writes to the Redis server as needed to deliver requests, receive responses, and receive server pushes."  
>  
> "Depending on the user's requirements, there are different styles of calling async_run. If there is only one active client communicating with the server, the easiest way to call async_run is to only run it simultaneously with each exec commands, thusly:"  
> co_await (con.async_run() || con.async_exec) && ...; // whatevs  
>  
> (and then we have to explain why this syntax looks like this, and the benefits of doing so which includes the best performance in terms of application-level TCP/IP flow control and backrpessure)  
>  
> "If there are many in-process clients performing simultaneous requests, an alternative is to launch a long-running coroutine which calls async_run:"  
> awaitable<void> do_run(connection& c)  
> {  
>   co_await c.async_run();  
> }  
>  
> (and then you have to give an example using callbacks)  
>  
> "While calling async_run is a sufficient condition for maintaining active two-way communication with the Redis server, most production deployments will want to do more. For example, they may want to reconnect if the connection goes down, either to the same server or a failover server. They may want to perform health checks. They may want to run for a certain amount of time (say, 1 second) and then briefly perform an algorithm which requires that no other threads are accessing shared data structures like the connection."  
>  
> "The library requires the caller to manually invoke async_run to allow these customizations."  
>  
> (and then show examples of health_checker and reconnect)  
>  
> This should be explained in both the README and the html docs, and it has to come first because it is a principal feature of the library that anyone who integrates it will need to be aware of.
