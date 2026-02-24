# #1399 - Invalid object access if a socket was destroyed with http::async_read pending [Closed]

> Username: mejedi  
> Created at: 2018-12-31 12:42:59 UTC  
> Updated at: 2019-03-21 03:40:55 UTC  
> Closed at: 2019-03-21 03:40:54 UTC  
> Url: https://github.com/boostorg/beast/issues/1399  

Beast versions 167, 181.  
  
If a socket was destroyed with `http::async_read` pending, a dead object (the socket) is accessed when dispatching the completion handler for `async_read` .  
  
[Crashing code sample.](https://gist.github.com/mejedi/da0d98e809b6c2d667fc3768a5897945)  
  
I wonder if this is a legitimate use or not.  
  
AFAIK, with ASIO it is permitted to destroy an object while there are asynchronous operations pending (provided that the completion handler won't do anything funny, of course), and boost::beast shouldn't be different.  
  
ASIO's documentation doesn't explicitly say that you can destroy objects while there are asynchronous operations pending, but you can infer it from the examples shipping with the documentation. For instance, most socket server examples declare `io_context` and `acceptor` in the same scope, meaning that `acceptor`'s lifetime isn't extended while there are pending asynchronous operations:   
  
```  
{  
  io_context context;  
  ip::tcp::aceptor acceptor(context, ...);  
}  
```  
  
There's a program state prior to running `context`'s destructor when `acceptor` has been destroyed, but the `acceptor`'s completion handler(s) are still pending.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-12-31 16:45:15 UTC  
> Url: https://github.com/boostorg/beast/issues/1399#issuecomment-450667189  

> ASIO's documentation doesn't explicitly say that you can destroy objects while there are asynchronous operations pending  
  
The Asio docs are... what is it... "terse, but accurate.". That which is not explicitly disallowed, is allowed.  
  
Asio objects can be destroyed because they are not really objects, they are just references to a separately allocated implementation object which is owned by the `io_context`.   
  
> boost::beast shouldn't be different.  
  
Well, it would be nice to work the same way, but I'm not sure that's possible. I'm certainly open to ideas, how might we fix this?

---

## Comment 2

> Username: vinniefalco  
> Created at: 2018-12-31 23:34:04 UTC  
> Url: https://github.com/boostorg/beast/issues/1399#issuecomment-450699067  

> with ASIO it is permitted to destroy an object while there are asynchronous operations pending   
  
Sometimes yes, sometimes no:  
  
_"A stream object must not be destroyed while there are pending asynchronous operations associated with it."_  
  
https://www.boost.org/doc/libs/1_69_0/doc/html/boost_asio/reference/ssl__stream/_stream.html

---

## Comment 3

> Username: djarek  
> Created at: 2019-01-01 10:04:36 UTC  
> Updated at: 2019-01-01 10:05:16 UTC  
> Url: https://github.com/boostorg/beast/issues/1399#issuecomment-450719960  

Note that the crash in the example code isn't caused by the stream becoming invalid, but actually because of the references to the other values (buffer/message) becoming invalid.  
This behavior is consistent with `asio::async_read` (the DynamicBuffer has to remain valid at least until the handler is invoked).  
  
The Networking TS specifies that:  
> Unless otherwise specified, the lifetime of arguments to initiating functions shall be treated as follows:  
> If the parameter has a pointer type or has a type of lvalue reference to non-const, the implementation  
>may assume the validity of the pointee or referent, respectively, until the completion handler is invoked.  
>[ Note: In other words, the program is responsible for guaranteeing the validity of the argument until  
the completion handler is invoked. — end note ]  
  
On top of that, the `async_read`/`async_write` algorithms require (`[buffer.reqmts.read.write]`) that buffers and the stream reference remain valid until the call to the completion handler.  
  
This suggests that the bug is in your code.

---

## Comment 4

> Username: mejedi  
> Created at: 2019-01-02 10:26:45 UTC  
> Updated at: 2019-01-02 10:30:05 UTC  
> Url: https://github.com/boostorg/beast/issues/1399#issuecomment-450829474  

@vinniefalco, @djarek thank you for the prompt responses, I didn't expect it during the holidays season!  
  
There's one important detail I should've stressed but I didn't — when the socket is destroyed, no thread has `io_context`'s `run()` (or equivalent) method running, hence no completion handlers are dispatched. The socket destruction is followed by context teardown — doesn't invoke handlers as well.  
  
> https://www.boost.org/doc/libs/1_69_0/doc/html/boost_asio/reference/io_context.html#boost_asio.reference.io_context.synchronous_and_asynchronous_operations  
  Handlers are invoked only by a thread that is currently calling any overload of run(), run_one(), run_for(), run_until(), poll() or poll_one() for the io_context.  
  
In the light of that  
  
@vinniefalco  
  
>  Well, it would be nice to work the same way, but I'm not sure that's possible. I'm certainly open to ideas, how might we fix this?  
  
I would expect that `boost::beast::http::async_read()` defers to `async_read()` of the underlying stream internally. The natural expectation would be that it performs processing in a completion handler attached to the underlying `async_read()`, invoking user's completion handler if a complete HTTP message was parsed, otherwise calling underlying `async_read()` again.  
  
Hence if we are in a situation when completion handlers aren't called, the completion handler attached to the underlying `async_read()` won't be called in the first place, hence it should be safe.  
  
@djarek  
> This behavior is consistent with asio::async_read (the DynamicBuffer has to remain valid at least until the handler is invoked).  
  
What if the completion handler isn't going to be called, ever? I've updated my [code sample](https://gist.github.com/mejedi/da0d98e809b6c2d667fc3768a5897945) to include `boost::asio::async_read()`. The later doesn't crash. Hardly proves anything, of course :)  
  
@vinniefalco, @djarek  
To summarise, I'm interested in a use case when a socket is destroyed with `http::async_read` still pending, while no thread has `io_context`'s `run()` (or equivalent) method running, followed by context teardown.  
  
* Do you think this is a valid use of the library (or "the bug is in your code")?  
* Is this use case important enough to be worth supporting?  
  
If you are open to making a change to the library to support it, I could probably produce a patch.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2019-01-02 15:15:15 UTC  
> Url: https://github.com/boostorg/beast/issues/1399#issuecomment-450889312  

> Do you think this is a valid use of the library (or "the bug is in your code")?  
  
If you call `io_context::stop` and subsequently join all the threads that were calling `io_context::run` (or equivalent), then it should be safe to destroy all the I/O objects.

---

## Comment 6

> Username: mejedi  
> Created at: 2019-01-02 16:28:35 UTC  
> Updated at: 2019-01-02 18:39:13 UTC  
> Url: https://github.com/boostorg/beast/issues/1399#issuecomment-450910802  

> If you call io_context::stop and subsequently join all the threads that were calling io_context::run (or equivalent), then it should be safe to destroy all the I/O objects.  
  
I confirm that all threads are joined in the "real" code. The sample I've shared previously is single-threaded and exhibits the crash as well. Along with the code I've posted a stack trace (quoting only the "interesting" excerpt):  
  
boost::asio::io_context::~io_context()  
...  
boost::asio::detail::scheduler_operation::destroy()  
...  
boost::beast::http::detail::read_some_op<boost::asio::basic_stream_socket<boost::asio::ip::tcp>, ...>::get_executor() const  
The later is defined as follows in Beast v181:  
  
    executor_type  
    get_executor() const noexcept  
    {  
        return (boost::asio::get_associated_executor)(  
            h_, s_.get_executor());  
    }  
In this template instantiation, s_ is a reference to a boost::asio::basic_stream_socket, which is already destroyed.  
  
I wonder if it is important to ask the Stream for the executor, instead of obtaining it once when read_some_op is created, subsequently returning a cached value.

---

## Comment 7

> Username: vinniefalco  
> Created at: 2019-01-02 17:58:36 UTC  
> Updated at: 2019-01-02 18:56:46 UTC  
> Url: https://github.com/boostorg/beast/issues/1399#issuecomment-450935011  

> I wonder if it is important to ask the Stream for the executor, instead of obtaining it once when read_some_op is created, subsequently returning a cached value.  
  
Great question and quite serendipituous! I am thinking about rolling this utility out to all of Beast's composed operations. Coincidentally, it caches the executor :)  
  
https://github.com/boostorg/beast/blob/8b72b4d407674176187f7427bd1e6a7c88192119/include/boost/beast/core/detail/operation_base.hpp#L27

---

## Comment 8

> Username: djarek  
> Created at: 2019-01-02 20:12:37 UTC  
> Url: https://github.com/boostorg/beast/issues/1399#issuecomment-450971591  

@mejedi while the case of an operation being abandoned is not explicitly defined, I believe we can infer from the spec that all objects "owned" by the handler have to remain valid until either of these happens:  
- the handler is invoked  
- the last "valid"(not invoked, not moved-from) handler is destroyed  
Note that it works with `asio::async_read` because asio uses the more verbose, non-intrusive version of the `associated_executor` customization point.  
  
TL;DR: Such a use of ASIO/Beast is UB. Destroying an I/O object before invocation/abandonment only works for "primitive" async operations.

---

## Comment 9

> Username: mejedi  
> Created at: 2019-01-07 22:48:46 UTC  
> Updated at: 2019-01-07 22:48:55 UTC  
> Url: https://github.com/boostorg/beast/issues/1399#issuecomment-452109961  

@djarek   
>  I believe we can infer from the spec...  
  
Could you support your claims with a reference to the relevant portion of the spec?

---

## Comment 10

> Username: vinniefalco  
> Created at: 2019-01-07 23:21:04 UTC  
> Url: https://github.com/boostorg/beast/issues/1399#issuecomment-452117807  

> Could you support your claims with a reference to the relevant portion of the spec?  
  
From:  
https://www.boost.org/doc/libs/1_69_0/doc/html/boost_asio/reference/asynchronous_operations.html#boost_asio.reference.asynchronous_operations.lifetime_of_initiating_function_arguments  
  
_"If the parameter has a pointer type or has a type of lvalue reference to non-const, the implementation may assume the validity of the pointee or referent, respectively, until the completion handler is invoked. "_

---

## Comment 11

> Username: vinniefalco  
> Created at: 2019-01-13 01:49:01 UTC  
> Url: https://github.com/boostorg/beast/issues/1399#issuecomment-453795144  

Is this resolved?

---

## Comment 12

> Username: stale[bot]  
> Created at: 2019-02-12 02:00:39 UTC  
> Url: https://github.com/boostorg/beast/issues/1399#issuecomment-462581850  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 13

> Username: vinniefalco  
> Created at: 2019-02-12 02:04:08 UTC  
> Url: https://github.com/boostorg/beast/issues/1399#issuecomment-462582519  

Relevant: http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2019/p1442r0.html#_underspecified_ownership_of_asynchronous_initiation_function_arguments

---

## Comment 14

> Username: stale[bot]  
> Created at: 2019-03-14 02:46:53 UTC  
> Url: https://github.com/boostorg/beast/issues/1399#issuecomment-472683550  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 15

> Username: stale[bot]  
> Created at: 2019-03-21 03:40:54 UTC  
> Url: https://github.com/boostorg/beast/issues/1399#issuecomment-475104666  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
