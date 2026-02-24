# #2591 - Question: strand of a strand? [Closed]

> Username: pfeatherstone  
> Created at: 2022-12-15 11:29:46 UTC  
> Updated at: 2023-02-05 19:22:26 UTC  
> Closed at: 2022-12-16 06:31:12 UTC  
> Url: https://github.com/boostorg/beast/issues/2591  

What is the behaviour when a strand is created from a strand. I.e.:  
```  
net::io_context ioc;  
auto strand_normal = net::make_strand(ioc);  
auto strand_weird   = net::make_strand(strand_normal);  
```  
?  
What's the difference in behaviour when work is bound to either of those strands?  
Thanks

---

## Comment 1

> Username: pfeatherstone  
> Created at: 2022-12-15 11:35:34 UTC  
> Updated at: 2022-12-15 11:37:42 UTC  
> Url: https://github.com/boostorg/beast/issues/2591#issuecomment-1352932475  

Basically if i have the code:  
```  
net::io_context ioc;  
tcp::acceptor acceptor(net::make_strand(ioc));  
acceptor.async_accept(beast::bind_front_handler(&listener::on_accept, shared_from_this()));  
  
...  
  
void on_accept(beast::error_code ec, tcp::socket socket)  
{  
    // socket's default executor is "acceptor"'s strand  
    std:make_shared<session>(std::move(socket));  
}  
...  
struct session  
{  
    net::strand<any_io_executor> read_strand;  
    net::strand<any_io_executor> write_strand;  
    tcp::socket sock;  
  
    session(tcp::socket sock_)   
    : sock{std::move(sock_)),   
      read_strand{sock.get_executor()},  // sock.get_executor() is acceptor's strand  
      write_strand{sock.get_executor()} {} // sock.get_executor() is acceptor's strand  
};  
```  
and `acceptor`'s default executor is a strand, then `read_strand` and `write_strand` are strands of strand right?

---

## Comment 2

> Username: pfeatherstone  
> Created at: 2022-12-15 11:39:09 UTC  
> Url: https://github.com/boostorg/beast/issues/2591#issuecomment-1352936513  

I mean i could replace   
```  
acceptor.async_accept(beast::bind_front_handler(&listener::on_accept, shared_from_this()));  
```  
with  
```  
acceptor.async_accept(ioc, beast::bind_front_handler(&listener::on_accept, shared_from_this()));  
```  
then the new socket's default executor is just plain old `net::io_context` instance. But i want to know what the difference in behaviour is.

---

## Comment 3

> Username: pfeatherstone  
> Created at: 2022-12-15 11:46:20 UTC  
> Url: https://github.com/boostorg/beast/issues/2591#issuecomment-1352943635  

If it's the case that creating strands from strands is really not desirable or undefined behaviour (?), then maybe the API should have constraints on what executors you should use. Maybe having a polymorphic executor type is really dangerous. Because `strand<any_io_executor>` could be anything. Maybe being specific about the underlying executor type is preferred.

---

## Comment 4

> Username: madmongo1  
> Created at: 2022-12-15 11:47:43 UTC  
> Url: https://github.com/boostorg/beast/issues/2591#issuecomment-1352944926  

Good question. I've never had a satisfactory answer. I would imagine it's similar to taking a lock on two mutexes before executing the completion handler rather than one.  
  
I agree that strands of polymorphic executors are problematic.   
  
Create your target strand from the ioc and pass it into async_accept.

---

## Comment 5

> Username: madmongo1  
> Created at: 2022-12-15 11:49:25 UTC  
> Url: https://github.com/boostorg/beast/issues/2591#issuecomment-1352946563  

You don't need a separate stand for read and write ops. You need one strand per discrete object, such as a connection.

---

## Comment 6

> Username: madmongo1  
> Created at: 2022-12-15 11:51:42 UTC  
> Url: https://github.com/boostorg/beast/issues/2591#issuecomment-1352948845  

```  
net::io_context ioc;  
tcp::acceptor acceptor(net::make_strand(ioc));  // you generally don't need a strand on an acceptor since only one thread will usually accept at once.  
  
acceptor.async_accept(net::make_strand(ioc),   // this will be the connection's strand.  
                      beast::bind_front_handler(&listener::on_accept, shared_from_this()));  
  
```

---

## Comment 7

> Username: pfeatherstone  
> Created at: 2022-12-15 11:52:50 UTC  
> Updated at: 2022-12-15 11:54:35 UTC  
> Url: https://github.com/boostorg/beast/issues/2591#issuecomment-1352949998  

> You don't need a separate stand for read and write ops. You need one strand per discrete object, such as a connection.  
  
Surely it's desirable to have separate read and write strands? For BSD sockets at least, `read()` and `write()` are atomic operations.

---

## Comment 8

> Username: madmongo1  
> Created at: 2022-12-15 11:54:51 UTC  
> Url: https://github.com/boostorg/beast/issues/2591#issuecomment-1352952020  

No. reads and writes on the same object can happen concurrently. But they should happen through the _same_ strand. Because you want them to be processed sequentially by the io object.   
  
The strand guards your entire connection and any timeouts etc it uses. you use it instead of a mutex to ensure that all operations involving your connection happen sequentially.

---

## Comment 9

> Username: pfeatherstone  
> Created at: 2022-12-15 11:56:09 UTC  
> Url: https://github.com/boostorg/beast/issues/2591#issuecomment-1352953367  

Yes i understand what a strand does. But i see no reason why read and write operations can't happen concurrently? All reads should be sequential and all writes should be sequential. But a read and a write can happen at the same time

---

## Comment 10

> Username: pfeatherstone  
> Created at: 2022-12-15 11:57:19 UTC  
> Url: https://github.com/boostorg/beast/issues/2591#issuecomment-1352954580  

It's like having a thread that does all the read operations and another thread that does all the write operations. According to BSD sockets docs, that's absolutely fine. Therefore, doing the same thing with Asio should be fine too.

---

## Comment 11

> Username: pfeatherstone  
> Created at: 2022-12-15 12:25:18 UTC  
> Url: https://github.com/boostorg/beast/issues/2591#issuecomment-1352986909  

Basically BSD sockets are reentrant.

---

## Comment 12

> Username: klemens-morgenstern  
> Created at: 2022-12-15 13:27:41 UTC  
> Url: https://github.com/boostorg/beast/issues/2591#issuecomment-1353067152  

it's not fine if you got something more complex than a socket though like any beast io object.  
This is also a question for asio, not beast.

---

## Comment 13

> Username: pfeatherstone  
> Created at: 2022-12-15 13:32:36 UTC  
> Url: https://github.com/boostorg/beast/issues/2591#issuecomment-1353072538  

Yes sorry. The asio repo is less responsive. I see asio and beast as the same thing now. Really http and websocket should be merged into asio. Sorry for spamming issues. A discussions tab would be ideal as this isn't an issue at all, it's a bunch of questions.   
  
I do feel like up to date course material is due. I still see online tutorials from as late as 2020 that start with "use a `asio::io_service`, when this is deprecated. Which makes me think that there is A LOT of material online which is really out of date. It's so easy to use asio incorrectly. In fact, it's probably easier to use it incorrectly than it is to use it correctly. So i feel like i  need to ask questions wherever i can in order to get best usage out of the library.

---

## Comment 14

> Username: pfeatherstone  
> Created at: 2022-12-15 13:39:10 UTC  
> Url: https://github.com/boostorg/beast/issues/2591#issuecomment-1353081793  

For example, i feel like making use of the default polymorphic executor is dangerous. From a code readability point of view, it's really not obvious what is running where. I feel like having explicit strands for different things (read, write, callbacks, etc), and using those strands explicitly via `post()` or `bind_executor()` makes it much clearer when reading the code where something is running. I also have a lot of `assert(strand.running_in_this_thread());` throughout my code to catch as many bugs during debug. But i don't see anyone else online promoting this. I tell myself, "maybe I'm doing something wrong". So i feel it best to just ask, even if it's obvious and even if there is something in the docs.

---

## Comment 15

> Username: madmongo1  
> Created at: 2022-12-15 20:03:35 UTC  
> Url: https://github.com/boostorg/beast/issues/2591#issuecomment-1353641878  

Every async operation on a beast websocket is a composed operation. Every async step in that operation touches the state of the websocket stream. It would therefore be inappropriate to have two handlers executing concurrently against the same stream.  
This is a good general principle to follow with any stream object.

---

## Comment 16

> Username: pfeatherstone  
> Created at: 2022-12-15 20:18:24 UTC  
> Url: https://github.com/boostorg/beast/issues/2591#issuecomment-1353659285  

Ok cool. Thank you everyone. Going back to first question. We don't know what a strand of a strand is ?

---

## Comment 17

> Username: pfeatherstone  
> Created at: 2022-12-15 20:22:34 UTC  
> Url: https://github.com/boostorg/beast/issues/2591#issuecomment-1353663353  

> Every async operation on a beast websocket is a composed operation. Every async step in that operation touches the state of the websocket stream. It would therefore be inappropriate to have two handlers executing concurrently against the same stream.  
>   
> This is a good general principle to follow with any stream object.  
>   
>    
  
In which case Asio and Beast should just enforce this. Like, make it impossible to compile unless you do exactly that.

---

## Comment 18

> Username: klemens-morgenstern  
> Created at: 2022-12-16 02:15:32 UTC  
> Url: https://github.com/boostorg/beast/issues/2591#issuecomment-1354083981  

> > Every async operation on a beast websocket is a composed operation. Every async step in that operation touches the state of the websocket stream. It would therefore be inappropriate to have two handlers executing concurrently against the same stream.  
> >   
> > This is a good general principle to follow with any stream object.  
> >   
> >    
>   
> In which case Asio and Beast should just enforce this. Like, make it impossible to compile unless you do exactly that.   
  
Would love to, can you tell me how to implement it?

---

## Comment 19

> Username: pfeatherstone  
> Created at: 2022-12-16 06:29:16 UTC  
> Url: https://github.com/boostorg/beast/issues/2591#issuecomment-1354282046  

Probably not :) Just an idea. The asio and beast source code looks quite complicated and the original authors would be better suited I think. I do feel like there is an opportunity to make some substantial changes to Asio/Beast to make it safer to use. The networking TS is dead so there is freedom to do what we want...

---

## Comment 20

> Username: pfeatherstone  
> Created at: 2022-12-16 06:30:38 UTC  
> Url: https://github.com/boostorg/beast/issues/2591#issuecomment-1354282894  

I'll close the issue as I think I've gone on a massive tangent. Many thanks to everyone

---

## Comment 21

> Username: pfeatherstone  
> Created at: 2022-12-16 19:22:18 UTC  
> Url: https://github.com/boostorg/beast/issues/2591#issuecomment-1355469679  

> > > Every async operation on a beast websocket is a composed operation. Every async step in that operation touches the state of the websocket stream. It would therefore be inappropriate to have two handlers executing concurrently against the same stream.  
> > >   
> > > This is a good general principle to follow with any stream object.  
> > >   
> > >    
> >   
> > In which case Asio and Beast should just enforce this. Like, make it impossible to compile unless you do exactly that.   
>   
> Would love to, can you tell me how to implement it?  
  
How about we make the default executor of a socket a strand, not an any_io_executor ? So it's impossible to construct a socket without giving it a strand. That would be a start

---

## Comment 22

> Username: sehe  
> Created at: 2022-12-17 01:33:56 UTC  
> Url: https://github.com/boostorg/beast/issues/2591#issuecomment-1355939144  

@pfeatherstone  that's also a pessimization.  
  
Besides, any_io_executor erases the executor type, which includes the underlying execution context. There's no such thing as a `any_strand` (`strand<io_context::executor_type>` is not the same as `strand<thread_pool::executor_type` etc.) and I'm pretty sure we wouldn't want to make one. It leads to situations where you could easily have multiply-type-erased executors, or multiply strand-wrapped executors, or both.  
  
Imagine constructing from an existing IO object: `mybeast_io_object(obj.get_executor())`; The `obj`'s executor would already be type-erased, so we can't know whether it includes a strand or needs wrapping. Do we blindly wrap it again?

---

## Comment 23

> Username: pfeatherstone  
> Created at: 2022-12-17 08:18:30 UTC  
> Url: https://github.com/boostorg/beast/issues/2591#issuecomment-1356115910  

I'm not suggesting we erase the strand. Force it to be strand<io_context::executor_type

---

## Comment 24

> Username: klemens-morgenstern  
> Created at: 2022-12-17 08:24:47 UTC  
> Url: https://github.com/boostorg/beast/issues/2591#issuecomment-1356118198  

`any_io_executor` erased the strand though.

---

## Comment 25

> Username: pfeatherstone  
> Created at: 2022-12-17 08:32:37 UTC  
> Updated at: 2022-12-17 18:59:24 UTC  
> Url: https://github.com/boostorg/beast/issues/2591#issuecomment-1356120392  

Yes but it can also erase anything else. So users can pass in any executor, even if it isn't best practice. I'm saying: if users should always pass in a strand via make_strand(ioc), why not enforce this in the library? Why bother having an erased executor when it will always and should always be the same type

---

## Comment 26

> Username: pfeatherstone  
> Created at: 2022-12-17 08:35:00 UTC  
> Updated at: 2022-12-17 09:54:46 UTC  
> Url: https://github.com/boostorg/beast/issues/2591#issuecomment-1356121114  

Somewhere above, I was suggesting passing ioc as the executor for the socket type then having two explicit strands, once for read and one for write. Then someone suggested that was a bad idea and should always pass make_strand(ioc) and use that for all handlers. So if that's best practice, why bother having any_io_executor as the default executor of the socket. That seems like a bad idea.

---

## Comment 27

> Username: pfeatherstone  
> Created at: 2022-12-17 18:58:08 UTC  
> Updated at: 2022-12-17 19:02:51 UTC  
> Url: https://github.com/boostorg/beast/issues/2591#issuecomment-1356385954  

I started using:  
```  
namespace safe  
{  
    using tcp_stream = beast::basic_stream<tcp, net::io_context::strand, beast::unlimited_rate_policy>;  
    using socket     = net::basic_stream_socket<tcp, net::io_context::strand>;  
    using acceptor   = net::basic_socket_acceptor<tcp, net::io_context::strand>;  
}  
```  
  
But there are too many objects inside asio and beast that use `any_io_executor` as default template parameter which user can't change.  
For example `websocket::stream<NextLayer, deflateSupported>` uses `net::steady_timer` but should be `net::basic_waitable_timer<chrono::steady_clock, boost::asio::wait_traits<chrono::steady_clock>, NextLayer::executor_type` to make this work.  
Nearly there though. Then hopefully, it would be impossible to use these objects without using a dedicated strand as default executor, which is inline with best practice. I'm all for libraries which make it hard for users to use incorrectly.

---

## Comment 28

> Username: sehe  
> Created at: 2022-12-17 19:04:21 UTC  
> Url: https://github.com/boostorg/beast/issues/2591#issuecomment-1356391035  

Yes you can do that. But the library cannot (should not) make that decision. Again, the choice for `strand<io_context::executor_type>` is arbitrary and limiting. But of course you can do that, and that's exactly why you can parameterize the `base_` object types.  
  
In fact this is a standard step when optimizing the performance of Asio code, as the type erasure isn't free.

---

## Comment 29

> Username: pfeatherstone  
> Created at: 2022-12-17 19:04:27 UTC  
> Updated at: 2022-12-17 19:06:42 UTC  
> Url: https://github.com/boostorg/beast/issues/2591#issuecomment-1356391078  

There is also the issue that `boost::asio::ip::tcp` assumes you're using `any_io_executor` and you can't change that

---

## Comment 30

> Username: pfeatherstone  
> Created at: 2022-12-17 19:05:10 UTC  
> Url: https://github.com/boostorg/beast/issues/2591#issuecomment-1356391260  

> Yes you can do that. But the library cannot (should not) make that decision. Again, the choice for `strand<io_context::executor_type>` is arbitrary and limiting. But of course you can do that, and that's exactly why you can parameterize the `base_` object types.  
>   
> In fact this is a standard step when optimizing the performance of Asio code, as the type erasure isn't free.  
  
Why shouldn't it make that decision if every asio expert says it's a bad idea to not use a strand?

---

## Comment 31

> Username: sehe  
> Created at: 2022-12-17 19:06:53 UTC  
> Url: https://github.com/boostorg/beast/issues/2591#issuecomment-1356391728  

Please slow down with the posting, and read more closely. It's not just choosing the strand wrapper. It's also hard coding the underlying context type. That's arbitrary and limiting.  
  
Also, mandating strands may not make sense in the context of, say, a single threaded application.

---

## Comment 32

> Username: sehe  
> Created at: 2022-12-17 19:09:52 UTC  
> Url: https://github.com/boostorg/beast/issues/2591#issuecomment-1356393356  

> For example websocket::stream<NextLayer, deflateSupported> uses net::steady_timer but should be net::basic_waitable_timer<chrono::steady_clock, boost::asio::wait_traits<chrono::steady_clock>, NextLayer::executor_type to make this work.  
  
That's a valid enhancement request, please file it.  
  
> boost::asio::ip::tcp assumes you're using any_io_executor   
  
But it doesn't. It merely provides the protocol, which you can use fine, as you have already just posted.  
  
> and you can't change that  
  
The code you just showed proves otherwise. The only thing you want to avoid is the convenience typedefs (like `tcp::socket`).

---

## Comment 33

> Username: pfeatherstone  
> Created at: 2022-12-17 19:11:16 UTC  
> Url: https://github.com/boostorg/beast/issues/2591#issuecomment-1356394161  

> The code you just showed proves otherwise. The only thing you want to avoid is the convenience typedefs (like tcp::socket).  
  
Ah ok, they are just convenience typedefs. They are not used in the internals of the object

---

## Comment 34

> Username: pfeatherstone  
> Created at: 2022-12-17 22:10:34 UTC  
> Url: https://github.com/boostorg/beast/issues/2591#issuecomment-1356486012  

> Also, mandating strands may not make sense in the context of, say, a single threaded application  
  
Interesting, do strands incur a runtime cost ?

---

## Comment 35

> Username: sehe  
> Created at: 2022-12-17 22:11:59 UTC  
> Updated at: 2022-12-17 22:12:15 UTC  
> Url: https://github.com/boostorg/beast/issues/2591#issuecomment-1356486315  

> Interesting, do strands incur a runtime cost ?  
  
Yes, in general.  
  
With the right hinting and compile time flags it **may** be reduced or eliminated in practice, but in principle synchronization is overhead.

---

## Comment 36

> Username: uohmak4fvpqe  
> Created at: 2023-02-05 19:16:12 UTC  
> Updated at: 2023-02-05 19:22:26 UTC  
> Url: https://github.com/boostorg/beast/issues/2591#issuecomment-1418242545  

> No. reads and writes on the same object can happen concurrently. But they should happen through the _same_ strand. Because you want them to be processed sequentially by the io object.  
  
I don't understand what you mean, this seems contradictory to me. Do they happen concurrently or they have to be sequential in one strand ?  
Do you mean the async_* can be concurrent but not the handlers ?  
  
> Every async operation on a beast websocket is a composed operation. Every async step in that operation touches the state of the websocket stream. It would therefore be inappropriate to have two handlers executing concurrently against the same stream.  
  
Would it be reasonnable to call this a design flaw ? Couldn't concurrent handling dramatically accelerate the flux of information ?  
It looks like beast sequentializes each connection whereas I would rather think of sequentializing each i/o direction with one strand for all input streams, one for all outputs streams. Putting a strand on each connexion makes littles sense to me.  
Based on what I know of beast now, I could even consider establishing two streams, and using one for input, the other for output, so as to finally have concurrent i/o
