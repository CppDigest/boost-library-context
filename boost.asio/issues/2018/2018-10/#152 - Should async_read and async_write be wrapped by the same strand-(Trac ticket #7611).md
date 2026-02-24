# #152 - Should async_read and async_write be wrapped by the same strand?(Trac ticket #7611) [Closed]

> Username: redboltz  
> Created at: 2018-10-12 07:44:50 UTC  
> Updated at: 2018-10-13 02:57:47 UTC  
> Closed at: 2018-10-13 02:57:47 UTC  
> Url: https://github.com/boostorg/asio/issues/152  

Continues from https://svn.boost.org/trac10/ticket/7611.  
  
I experienced the same segfault as https://svn.boost.org/trac10/attachment/ticket/7611/gdb.log in my big project that uses async_read and async_write. I'm using `boost::asio::ip::tcp::socket`.  
  
And the problem has been solved by the same way as https://svn.boost.org/trac10/ticket/7611#comment:15  
  
The point is as follows:  
  
>For what I understood about asio, it should be possible to read and write on the same socket from different threads without any problem. But if the close requires serialization, then all read/ write operations must be wrapped by a strand. And it will affect the performance (a bit).  
  
I'm not sure why async_read and async_write should be wrapped by the same strand. I guess that only async_write should be wapped by the strand but async_read doesn't need to be wrapped.  
  
I asked (commented) stackoverflow as follows:  
  
https://stackoverflow.com/questions/12794107/why-do-i-need-strand-per-connection-when-using-boostasio/12801042#comment79753079_12801042  
  
https://stackoverflow.com/questions/12794107/why-do-i-need-strand-per-connection-when-using-boostasio/12801042#comment79777780_12801042  
  
The conversation indicates that   
  
https://www.boost.org/doc/libs/1_68_0/doc/html/boost_asio/reference/ip__tcp/socket.html#boost_asio.reference.ip__tcp.socket.thread_safety  
  
**"Shared objects: Unsafe."**   
  
is the reason for async_read and async_write should be wrapped by the same strand.  
  
Is that right?  
  
@mjcaisse uses strand for only async_write in his presentation at CppCon2016.  
https://www.youtube.com/watch?v=rwOv_tw2eA4&t=2853  
  
Then I asked him about this issue. He think that only async_write requires the strand, not both async_read and async_write. And at that time, he advised me that I should ask to @vinniefalco. So I post this issue here.  
  
I'd like to clarify which is correct.  
  
NOTE:   
I couldn't reproduce the issue by  
https://svn.boost.org/trac10/attachment/ticket/7611/asio_bug.cpp

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-10-12 16:33:57 UTC  
> Url: https://github.com/boostorg/asio/issues/152#issuecomment-429385320  

All you need to know right here:  
**"Shared objects: Unsafe."**  
  
This includes functions like `cancel` and `close` (and the destructor, obviously).

---

## Comment 2

> Username: cmazakas  
> Created at: 2018-10-12 16:42:47 UTC  
> Url: https://github.com/boostorg/asio/issues/152#issuecomment-429387869  

> I'm not sure why async_read and async_write should be wrapped by the same strand. I guess that only async_write should be wapped by the strand but async_read doesn't need to be wrapped.  
  
In Asio, asynchronous operations use the executor associated with the supplied handler. In this case, both of the callbacks to the `async_read` and `async_write` functions need to be on the same strand because the `io_context` can potentially run operations on two threads which both touch the same object (in this case, your `socket).  
  
This is only safe if only reads are performed but this is not the case.  
  
So you need a mechanism of serialization. If you run the `io_context` on just one thread, you have a global implicit strand so you don't need an explicit `asio::strand`.  
  
Otherwise, you need to serialize the invocation of your callbacks using an explicit `asio::strand` which will also properly serialize any async operations that `async_read` and `async_write` perform themselves. This is a passive benefit that comes from Asio's async and executor model (which is why it's also such a thing of beauty).  
  
Strands are basically thread-safe work queues that make sure you avoid race conditions.

---

## Comment 3

> Username: mjcaisse  
> Created at: 2018-10-12 16:51:21 UTC  
> Url: https://github.com/boostorg/asio/issues/152#issuecomment-429390404  

Apparently I need to update some slides. I should probably also make comments in the existing videos so people don't stumble on this.

---

## Comment 4

> Username: redboltz  
> Created at: 2018-10-13 02:57:47 UTC  
> Url: https://github.com/boostorg/asio/issues/152#issuecomment-429505719  

Thank you all!  
I have already used the same strand for async_read and async_write in my production code, but I was not 100% sure that I understand correctly why it is needed.  
  
Now, I understand it.   
  
> Apparently I need to update some slides. I should probably also make comments in the existing videos so people don't stumble on this.  
  
@mjcaisse , your presentations are always very easy to understand. If you update or add comments for the slides, it is very helpful for people.
