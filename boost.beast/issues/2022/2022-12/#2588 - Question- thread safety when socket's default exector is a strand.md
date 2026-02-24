# #2588 - Question: thread safety when socket's default exector is a strand [Closed]

> Username: pfeatherstone  
> Created at: 2022-12-14 09:37:27 UTC  
> Updated at: 2022-12-16 08:38:10 UTC  
> Closed at: 2022-12-16 08:38:10 UTC  
> Url: https://github.com/boostorg/beast/issues/2588  

I have the following:  
```  
struct some_listner_object  
{  
tcp::acceptor acceptor;  
...  
void do_accept(  
  acceptor.async_accept(  
                net::make_strand(ioc),  
                [self=shared_from_this(), this](beast::error_code ec, tcp::socket socket)  
                {  
                    if(ec)  
                        return;  
                      
                    std::make_shared<some_session_object_with_socket>(std::move(socket))->start();  
  
                    do_accept();  
                }  
  );  
};  
```  
  
By using `make_strand`, i'm hoping the socket's default executor is a strand. So hopefully, all **async operations** on the socket are by default executed on the strand. Or is it by default all **completion handlers** are executed on the strand?  
Basically, if it's the former, then all async operations are then thread safe. If it's the later, then all async operations need to be first `post`ed, unless they are already being called on the strand.

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2022-12-14 09:47:39 UTC  
> Url: https://github.com/boostorg/beast/issues/2588#issuecomment-1350749147  

That code compiles?

---

## Comment 2

> Username: pfeatherstone  
> Created at: 2022-12-14 09:50:59 UTC  
> Url: https://github.com/boostorg/beast/issues/2588#issuecomment-1350754516  

Yep. I have omitted most of the program though. I just wanted to show the crucial bit : `acceptor.async_accept(net::make_strand(ioc), ...)`

---

## Comment 3

> Username: pfeatherstone  
> Created at: 2022-12-14 09:52:38 UTC  
> Url: https://github.com/boostorg/beast/issues/2588#issuecomment-1350757239  

Basically, i want to know if a socket's default executor is a strand, then is everything always, automatically thread-safe ? Because all async operations are initiated on that strand and all completion handlers are called on that strand?

---

## Comment 4

> Username: pfeatherstone  
> Created at: 2022-12-14 09:55:55 UTC  
> Url: https://github.com/boostorg/beast/issues/2588#issuecomment-1350762609  

If an external thread is calling a function which will call an async function on a socket, then does it have to be posted first to the socket's default executor, i.e. it's strand, or will that async function automatically be initiated on the default executor, i.e. the strand? Do you see what i mean?   
I'm getting confused as to when async reads, writes, accepts, etc, need first to be posted or dispatched if the socket's default executor is a strand.

---

## Comment 5

> Username: pfeatherstone  
> Created at: 2022-12-14 10:00:19 UTC  
> Url: https://github.com/boostorg/beast/issues/2588#issuecomment-1350769924  

Ok, another maybe clearer question. Does the executor dictate where the async function is **initiated** or does it only dictate where the completion handler of that sync function is run?

---

## Comment 6

> Username: criatura2  
> Created at: 2022-12-14 13:03:53 UTC  
> Url: https://github.com/boostorg/beast/issues/2588#issuecomment-1351317488  

> context  
>   An execution context which provides the I/O executor that **the socket will use**, by default, **to dispatch handlers for any asynchronous operations performed on the socket.**   
  
https://www.boost.org/doc/libs/1_80_0/doc/html/boost_asio/reference/basic_stream_socket/basic_stream_socket/overload2.html

---

## Comment 7

> Username: pfeatherstone  
> Created at: 2022-12-14 14:39:49 UTC  
> Url: https://github.com/boostorg/beast/issues/2588#issuecomment-1351539892  

So does that mean i never have to use `dispatch()` or `post` on the strand? I can just use the async functions and everything is always thread safe ?

---

## Comment 8

> Username: mzimbres  
> Created at: 2022-12-15 08:44:24 UTC  
> Updated at: 2022-12-15 10:02:05 UTC  
> Url: https://github.com/boostorg/beast/issues/2588#issuecomment-1352730337  

https://www.boost.org/doc/libs/1_81_0/doc/html/boost_asio/reference/ip__tcp/socket.html#boost_asio.reference.ip__tcp.socket.thread_safety  
  
AFAIS, to call async member functions across executors you need to dispatch (or post).
