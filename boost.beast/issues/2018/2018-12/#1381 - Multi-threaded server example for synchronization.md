# #1381 - Multi-threaded server example for synchronization [Closed]

> Username: bandzaw  
> Created at: 2018-12-20 15:31:08 UTC  
> Updated at: 2025-02-06 23:45:56 UTC  
> Closed at: 2023-12-31 10:16:30 UTC  
> Url: https://github.com/boostorg/beast/issues/1381  

I use Boost.Beast WebSockets version 1.68 for a multihreaded application that I'm currently developing.  
  
Unfortunately I just got the "Assertion `id_ != T::id' failed" from the try_lock function in boost_1_68_0/boost/beast/websocket/detail/stream_base.hpp:105 under heavy load.  
  
To my surprise this is how it obviously is supposed to work; the comment in the source code of try_lock function clearly says that this will happen if I call an async function before the previous call to the same async function has *completed*.  
  
I therefore thought that a quick fix would be to guard the call to the async function with a mutex:  
  
```  
void WSSession::send(std::shared_ptr<uint8_t[]> data, size_t length)    
{  
    std::lock_guard<std::mutex> lck(write_mutex_);  
    ws_.async_write(    
        boost::asio::buffer(data.get(), length),  
        boost::asio::bind_executor(  
            strand_,  
            [data](boost::system::error_code ec, std::size_t bytes_transferred)  
            {  
                ...  
            }  
        )  
    );  
}  
```  
But then I get the same problem still. Hmm, does _completed_ in the code's comment really mean that the handler must have been called before I'm allowed to call the async function again!? Let's try:  
```  
void WSSession::send(std::shared_ptr<uint8_t[]> data, size_t length)    
{  
    write_mutex_.lock();  
    ws_.async_write(    
        boost::asio::buffer(data.get(), length),  
        boost::asio::bind_executor(  
            strand_,  
            [&write_mutex_,data](boost::system::error_code ec, std::size_t bytes_transferred)  
            {  
                ...  
                write_mutex_.unlock();  
            }  
        )  
    );  
}  
```  
Now this works - yay!  
  
But this solution feels like overkill, I could as well use the synchronous API if I have to add a mutex and handle its locking and unlocking manually. Also, I have not seen this convoluted construction before, i.e. locking a mutex in a async function and unlocking it in its handler, which clearly is a sign that I'm doing something stupid...  
  
But... I thought that all of this would be automagically handled by the strand that I'm using, that's the whole purpose of it! The documentation of strand states:   
  
> A strand is defined as a strictly sequential invocation of event handlers (i.e. no concurrent invocation). Use of strands allows execution of code in a multithreaded program without the need for explicit locking (e.g. using mutexes).  
  
So my question is, do I really need both belt and suspenders, or is this maybe a bug in the strand implementation or perhaps in its documentation, because clearly the strand does not allow for the execution of code in my multithreaded program without the need for explicit locking using mutexes!? However, I think it is more likely that I have missed something fundamental - but what?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-12-20 15:48:14 UTC  
> Updated at: 2018-12-20 15:48:41 UTC  
> Url: https://github.com/boostorg/beast/issues/1381#issuecomment-449042488  

> I think it is more likely that I have missed something fundamental - but what?  
  
This.  
  
TCP/IP streams and the I/O objects built with them (`ssl::stream` and `websocket::stream` for example) can realistically only perform one write and one read simultaneously. The following is undefined behavior:  
```  
    sock.async_write_some(buffer1, my_handler);  
    sock.async_write_some(buffer2, my_handler);  
```  
  
The code above initiates two of the same type of outstanding asynchronous operations simultaneously. Strands are not intended to solve this problem. Rather, they are intended to solve the problem of **completion handlers** executing concurrently. In the case of this example code, it is that there are two of the same **I/O operations** on the same socket executing concurrently. This is what is disallowed.  
  
Fortunately, solving this problem is trivial. You just need to implement a queue. When you go to send a message, store it in the queue and remove it when the message is done sending. Every time you send a message, check the queue and see if it has anything in it. If so, then add the new message to the queue but do not send it right away (because there is already a message being sent). Then, every time a message finishes sending, remove it then check the queue if there is another message and send it if so.  
  
To re-iterate, a strand is not intended to be a queue for storing your outgoing messages. You need to implement that yourself. The Beast example "CppCon2018" demonstrates how to do this:  
  
https://github.com/boostorg/beast/blob/c4813a5ac79d802951cf70b811ded94ce7ef7d79/example/cppcon2018/websocket_session.cpp#L87  
  
There is also a video presentation which goes through this example from top to bottom and explains how and why everything is done:  
  
https://www.youtube.com/watch?v=7FQwAjELMek  
  
You should not need a mutex. Notice how in the CppCon2018 example code, there is no mutex which protects the queue. Instead, synchronization is achieved through the implicit strand (only one thread calling `io_context::run`). Nothing ever executes concurrently.  
  
If you have any questions feel free to add them here or open a new issue.  
  
Good luck!

---

## Comment 2

> Username: bandzaw  
> Created at: 2018-12-20 23:20:39 UTC  
> Url: https://github.com/boostorg/beast/issues/1381#issuecomment-449170299  

Thank you for your detailed answer Vinnie! I’ll check into this tomorrow :-)

---

## Comment 3

> Username: bandzaw  
> Created at: 2018-12-21 08:24:17 UTC  
> Url: https://github.com/boostorg/beast/issues/1381#issuecomment-449307536  

Unfortunately the trivial solution to this problem is not that trivial in a multithreaded environment. I not only need to introduce a queue, but I also need to protect it from concurrent access. Since we do not have a MT-safe queue in our language I need to construct it and use that mutex anyway.   
  
My simple and hack-ish solution described above where I manually lock and unlock the mutex, at least has no queue. But it will stall threads waiting for async write access, which may or may not be a problem.  
  
I guess this is not a Boost.Beast specific "problem" but it is inherited from Boost.Asio? I must say that I'm a bit surprised that the forthcoming networking standard forces users to manually serialize access to the async API and that feels very much suboptimal.

---

## Comment 4

> Username: zlojvavan  
> Created at: 2018-12-21 10:28:04 UTC  
> Url: https://github.com/boostorg/beast/issues/1381#issuecomment-449351003  

> Unfortunately the trivial solution to this problem is not that trivial in a multithreaded environment. I not only need to introduce a queue, but I also need to protect it from concurrent access. Since we do not have a MT-safe queue in our language I need to construct it and use that mutex anyway.  
>   
perhaps you could use boost::lockfree queue (or any other implementation of your choice) instead of mutex if you want to avoid it?

---

## Comment 5

> Username: bandzaw  
> Created at: 2018-12-21 12:10:39 UTC  
> Url: https://github.com/boostorg/beast/issues/1381#issuecomment-449372868  

Sure, but I do not really like this added complexity. For example, if a sender cannot send the message right now because there is already an ongoing transmission and therefore decides to put the outgoing message into the queue. But what if the handler function just removed its message from queue after the sender checked it and then checked the queue for more outgoing messages the moment before the sender adds the new message into the queue? In that case, new outgoing messages will never be sent... Clearly access to the queue must be synchronized.  
  
Anyway, I have, at least I had, the feeling that this should not be all this messy. I use strands explicitly with the belief that they solve the concurrency issues as long as all async handlers go through the same strand. When re-reading the [strands documentation](https://www.boost.org/doc/libs/1_69_0/doc/html/boost_asio/overview/core/strands.html) it starts with the nice comforting headline: **_Use threads without explicit locking_** and I cannot see anything else that would make me think otherwise.  
  
However, when I read the [using websocket documentation](https://www.boost.org/doc/libs/1_69_0/libs/beast/doc/html/beast/using_websocket/send_and_receive_messages.html) it clearly says that:   
> websocket::stream is not thread-safe. Calls to stream member functions must all be made from the same implicit or explicit strand.  
  
So, if I read this correctly, not only handlers should go through the same strand but also all calls to websocket::stream member functions?

---

## Comment 6

> Username: zlojvavan  
> Created at: 2018-12-21 12:29:32 UTC  
> Url: https://github.com/boostorg/beast/issues/1381#issuecomment-449376619  

> For example, if a sender cannot send the message right now because there is already an ongoing transmission and therefore decides to put the outgoing message into the queue. But what if the handler function just removed its message from queue after the sender checked it and then checked the queue for more outgoing messages the moment before the sender adds the new message into the queue? In that case, new outgoing messages will never be sent... Clearly access to the queue must be synchronized  
  
you may want to utilize atomic flag/counter indicating whether the message should be dispatched to send immediately or put into lf queue to be extracted later within completion handler

---

## Comment 7

> Username: vinniefalco  
> Created at: 2018-12-21 13:46:13 UTC  
> Url: https://github.com/boostorg/beast/issues/1381#issuecomment-449392440  

> I not only need to introduce a queue, but I also need to protect it from concurrent access  
  
You are way over-thinking this :) Just post your queue operation to the strand:  
  
```  
// Send a message to the remote peer.  
// May be called from any thread.  
void websocket_session::send(std::shared_ptr<std::string const> const& message)  
{  
    // Get on the strand if we aren't already on it  
    if(! strand_.running_in_this_thread())  
        return net::post(  
                strand_,  
                std::bind(  
                    &websocket_session::send  
                    this->shared_from_this(),  
                    message));  
  
    // Always add to queue  
    queue_.push_back(message);  
  
    // Are we already writing?  
    if(queue_.size() > 1)  
        return;  
  
    // We are not currently writing, so send this immediately  
    ws_.async_write(  
        net::buffer(*queue_.front()),  
        net::bind_executor(  
            strand_,  
            std::bind(  
                &websocket_session::on_write,  
                shared_from_this(),  
                std::placeholders::_1,  
                std::placeholders::_2));  
}  
```  
  
I really need to add a multi-threaded example so people can see how this is done, I'll update the issue.  
  
>  if I read this correctly, not only handlers should go through the same strand but also all calls to websocket::stream member functions?  
  
Yes, that is correct. And this is not just Beast but for Asio and I/O objects in general. If you look at the bottom of the documentation page you will see "Shared objects: Unsafe" under Thread Safety:  
  
<https://www.boost.org/doc/libs/1_69_0/doc/html/boost_asio/reference/basic_stream_socket.html>  
  
Unless otherwise specified, **no class members are thread-safe**. This includes special members, especially the destructor!  
  
> you may want to utilize atomic flag/counter  
  
Atomic variables and lock-free techniques are really unnecessary here. Just post your operations to the strand as I have shown, this can be made to enable any member function to become thread safe as long as you capture the function argument's correctly when reposting the call to the strand (being particularly mindful of object lifetimes).

---

## Comment 8

> Username: bandzaw  
> Created at: 2018-12-21 15:34:05 UTC  
> Url: https://github.com/boostorg/beast/issues/1381#issuecomment-449419176  

Yes, that's quite nice. Your last code snippet shows what I concluded and ended up coding myself. But that I really needed to do all this was not obvious at all to me when I started to develop my app.   
  
It would be great if the library could handle all that boilerplate, allowing me to focus on my application instead of this low-level stuff :-)  
  
Thanks Vinnie, always helpful!

---

## Comment 9

> Username: vinniefalco  
> Created at: 2018-12-21 15:41:14 UTC  
> Url: https://github.com/boostorg/beast/issues/1381#issuecomment-449421199  

> It would be great if the library could handle all that boilerplate  
  
Well, I don't know that I would call this boilerplate. Synchronizaton is a "cross-cutting concern" [1] and not something that is easily abstracted in boilerplate. Concurrent programs can be difficult to write and reason about, and often they don't compose easily. For example if you have a few functions which each acquire a mutex to synchronize, it is often the case that they cannot be combined in a simple fashion without producing deadlocks or undefined behavior.  
  
Generally speaking, if you are going to introduce concurrency into your program then you have to be aware of its impact in everything that you write, and make adjustments accordingly. Libraries can make it easier to perform those adjustments (for example, by providing the `strand` model) but they can't make it disappear completely.  
  
[1] <https://stackoverflow.com/questions/23700540/cross-cutting-concern-example>

---

## Comment 10

> Username: vinniefalco  
> Created at: 2018-12-21 15:43:25 UTC  
> Url: https://github.com/boostorg/beast/issues/1381#issuecomment-449421835  

> ...that I really needed to do all this was not obvious at all to me when I started to develop my app.  
  
Based on my experiences and observations, it really does look like we are missing crucial documentation and tutorials on asynchronous networking concepts and idioms. The Asio docs are okay but they could be a lot better. That said, I don't think we should put the burden of this all on the Asio author. We need to take collective responsibility for it and produce this literature ourselves. I've done some of that in Beast but clearly I need to go farther. I'm working on it :)

---

## Comment 11

> Username: zlojvavan  
> Created at: 2018-12-24 07:11:43 UTC  
> Url: https://github.com/boostorg/beast/issues/1381#issuecomment-449695517  

> Atomic variables and lock-free techniques are really unnecessary here. Just post your operations to the strand as I have shown  
  
sure, there's more than one way to skin a cat. I'm not sure though whether suggested approach while likely being more straightforward is also more lightweight resource wise. asio::post is kind of blackbox here

---

## Comment 12

> Username: klemens-morgenstern  
> Created at: 2022-09-24 06:09:04 UTC  
> Url: https://github.com/boostorg/beast/issues/1381#issuecomment-1256876518  

What's the status of this issue?
