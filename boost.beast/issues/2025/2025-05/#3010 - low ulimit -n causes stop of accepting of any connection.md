# #3010 - low ulimit -n causes stop of accepting of any connection [Closed]

> Username: gitGameStory  
> Created at: 2025-05-24 12:19:08 UTC  
> Updated at: 2025-05-29 01:52:14 UTC  
> Closed at: 2025-05-29 01:52:14 UTC  
> Url: https://github.com/boostorg/beast/issues/3010  

During perfomance testing we hit an error "too many files opened"  
After that server just stops accepting any connection.  
No exceptions are thrown.  
Tested example multi-chat.  
Cent OS 10  
  
_Vinnie, thanx for amazing library_

---

## Comment 1

> Username: gitGameStory  
> Created at: 2025-05-24 19:02:18 UTC  
> Url: https://github.com/boostorg/beast/issues/3010#issuecomment-2906979019  

to reproduce it i think it is fine to run chat-multi  
and modify async websocket client from examples to run 3000+ instances (if ulimit -n is 1024 (default)).  
the thing is after that error chat-multi just stops accepting any connection, and if we will try to run client again none of 3000 request will be handled

---

## Comment 2

> Username: vinniefalco  
> Created at: 2025-05-24 19:39:18 UTC  
> Url: https://github.com/boostorg/beast/issues/3010#issuecomment-2906992444  

Well this is normal. Do you expect beast to be able to overcome a system-mandated limit of open sockets?

---

## Comment 3

> Username: gitGameStory  
> Created at: 2025-05-24 20:07:26 UTC  
> Updated at: 2025-05-24 20:14:16 UTC  
> Url: https://github.com/boostorg/beast/issues/3010#issuecomment-2907002449  

Vinnie,  thanx for answer.  
I dont say about overcome, because its normal behaviour for overcome.  
But i say about continious behaviour, when all thoese overcome is gone, but it still doesnt accept connection.

---

## Comment 4

> Username: gitGameStory  
> Created at: 2025-05-24 20:11:51 UTC  
> Updated at: 2025-05-24 20:13:42 UTC  
> Url: https://github.com/boostorg/beast/issues/3010#issuecomment-2907003855  

I mean that overcome may be triggers some invalid state   
Where further accept is impossible

---

## Comment 5

> Username: vinniefalco  
> Created at: 2025-05-24 20:26:34 UTC  
> Url: https://github.com/boostorg/beast/issues/3010#issuecomment-2907008545  

> when all thoese overcome is gone, but it still doesnt accept connection.  
  
Do you have a bunch of sockets stuck in TIME_WAIT? https://totozhang.github.io/2016-01-31-tcp-timewait-status/

---

## Comment 6

> Username: gitGameStory  
> Created at: 2025-05-24 20:34:08 UTC  
> Updated at: 2025-05-24 20:34:44 UTC  
> Url: https://github.com/boostorg/beast/issues/3010#issuecomment-2907011025  

Im not 100% sure  
But does it lead to a situation when server just doesnt accept any connection in following 30mins + ?

---

## Comment 7

> Username: ashtum  
> Created at: 2025-05-24 20:34:43 UTC  
> Updated at: 2025-05-24 20:37:10 UTC  
> Url: https://github.com/boostorg/beast/issues/3010#issuecomment-2907011218  

@gitGameStory, the acceptor loop exits when any error occurs:  
https://github.com/boostorg/beast/blob/916dcf8eb3eff582356f7b1849446a3893da6eac/example/websocket/server/chat-multi/listener.cpp#L87-L88  
You can modify it to continue accepting connections when a `too many files opened` error happens.  
Like:  
```C++  
void  
listener::  
on_accept(beast::error_code ec, tcp::socket socket)  
{  
    if(ec)  
    {  
        if(ec != asio::error::no_descriptors)  
            return fail(ec, "accept");  
    }  
    else  
    {  
        // Launch a new session for this connection  
        boost::make_shared<http_session>(  
            std::move(socket),  
            state_)->run();  
    }  
  
    // The new connection gets its own strand  
    acceptor_.async_accept(  
        net::make_strand(ioc_),  
        beast::bind_front_handler(  
            &listener::on_accept,  
            shared_from_this()));  
}  
```

---

## Comment 8

> Username: gitGameStory  
> Created at: 2025-05-24 20:44:34 UTC  
> Updated at: 2025-05-24 20:45:08 UTC  
> Url: https://github.com/boostorg/beast/issues/3010#issuecomment-2907014220  

> [@gitGameStory](https://github.com/gitGameStory), the acceptor loop exits when any error occurs:  
>   
> [beast/example/websocket/server/chat-multi/listener.cpp](https://github.com/boostorg/beast/blob/916dcf8eb3eff582356f7b1849446a3893da6eac/example/websocket/server/chat-multi/listener.cpp#L87-L88)  
>   
> Lines 87 to 88 in [916dcf8](/boostorg/beast/commit/916dcf8eb3eff582356f7b1849446a3893da6eac)  
>   
>  if(ec)   
>      return fail(ec, "accept");   
>   
> You can modify it to continue accepting connections when a `too many files opened` error happens.  
> Like:  
> void  
> listener::  
> on_accept(beast::error_code ec, tcp::socket socket)  
> {  
>     if(ec)  
>     {  
>         if(ec != asio::error::no_descriptors)  
>             return fail(ec, "accept");  
>     }  
>     else  
>     {  
>         // Launch a new session for this connection  
>         boost::make_shared<http_session>(  
>             std::move(socket),  
>             state_)->run();  
>     }  
>   
>     // The new connection gets its own strand  
>     acceptor_.async_accept(  
>         net::make_strand(ioc_),  
>         beast::bind_front_handler(  
>             &listener::on_accept,  
>             shared_from_this()));  
> }  
  
ashtum, thanxfor your answer!  
  
but what does it change ?  
i mean your code doesnt do anything special. Its just changes individual on_accept  
Just read it please: when `too many files opened` error happens. beast switches to some wiered, when further on_accept are never called

---

## Comment 9

> Username: ashtum  
> Created at: 2025-05-24 20:59:37 UTC  
> Url: https://github.com/boostorg/beast/issues/3010#issuecomment-2907018913  

If I understand correctly, you're asking why that example doesn't accept a new connection after a single `too many files opened` error occurs.  
With that change, the accept operation will continue, and it will successfully accept a new connection if one of the previous connections is dropped and the number of open files falls below your process limit.

---

## Comment 10

> Username: gitGameStory  
> Created at: 2025-05-24 21:16:35 UTC  
> Url: https://github.com/boostorg/beast/issues/3010#issuecomment-2907036420  

> If I understand correctly, you're asking why that example doesn't accept a new connection after a single `too many files opened` error occurs. With that change, the accept operation will continue, and it will successfully accept a new connection if one of the previous connections is dropped and the number of open files falls below your process limit.  
  
well,  your code assumes that in my case fail(ec, "accept");  is called. but its not

---

## Comment 11

> Username: gitGameStory  
> Created at: 2025-05-24 21:25:06 UTC  
> Updated at: 2025-05-24 21:25:30 UTC  
> Url: https://github.com/boostorg/beast/issues/3010#issuecomment-2907060544  

> If I understand correctly, you're asking why that example doesn't accept a new connection after a single `too many files opened` error occurs. With that change, the accept operation will continue, and it will successfully accept a new connection if one of the previous connections is dropped and the number of open files falls below your process limit.  
  
Ill try to make some minimal client + server example that will reproduce.. where shoul i send it ?

---

## Comment 12

> Username: ashtum  
> Created at: 2025-05-24 21:34:08 UTC  
> Url: https://github.com/boostorg/beast/issues/3010#issuecomment-2907084620  

> well, your code assumes that in my case fail(ec, "accept"); is called. but its not  
  
Because you wrote:  
> During perfomance testing we hit an error "too many files opened"

---

## Comment 13

> Username: ashtum  
> Created at: 2025-05-24 21:34:16 UTC  
> Updated at: 2025-05-24 21:45:55 UTC  
> Url: https://github.com/boostorg/beast/issues/3010#issuecomment-2907084919  

> Ill try to make some minimal client + server example that will reproduce.. where shoul i send it ?  
  
You can create a repo or use gist.github.com.

---

## Comment 14

> Username: gitGameStory  
> Created at: 2025-05-24 21:39:58 UTC  
> Url: https://github.com/boostorg/beast/issues/3010#issuecomment-2907098980  

> > well, your code assumes that in my case fail(ec, "accept"); is called. but its not  
>   
> Because you wrote:  
>   
> > During perfomance testing we hit an error "too many files opened"  
  
Ashtum, thanx for your answer.  
See... it happens only once.  
and then on_accept is never called.  
aint it a result of incorrect error handling inside library ?

---

## Comment 15

> Username: vinniefalco  
> Created at: 2025-05-24 22:59:40 UTC  
> Url: https://github.com/boostorg/beast/issues/3010#issuecomment-2907308614  

> beast switches to some wiered  
  
Just to be clear, the examples are not "beast." Only the library code (stuff in include/boost/beast) is "beast." The examples are there to serve as a guide, but you need to adjust them to reflect your use-case. I did not test the examples under low values for ulimit.

---

## Comment 16

> Username: ashtum  
> Created at: 2025-05-25 06:07:31 UTC  
> Updated at: 2025-05-25 06:10:24 UTC  
> Url: https://github.com/boostorg/beast/issues/3010#issuecomment-2907634180  

> Ashtum, thanx for your answer. See... it happens only once. and then on_accept is never called. aint it a result of incorrect error handling inside library ?  
  
Have you tried the change I suggested above?  
```C++  
        if(ec != asio::error::no_descriptors)  
            return fail(ec, "accept");  
```

---

## Comment 17

> Username: gitGameStory  
> Created at: 2025-05-25 09:24:09 UTC  
> Url: https://github.com/boostorg/beast/issues/3010#issuecomment-2907714309  

Yes, thanx.  
It helped!
