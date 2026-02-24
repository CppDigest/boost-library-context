# #1142 - ThreadSanitizer reports data race when using a thread pool for io_context [Closed]

> Username: kirakirapower  
> Created at: 2018-05-26 10:11:21 UTC  
> Updated at: 2019-12-29 00:39:49 UTC  
> Closed at: 2018-07-16 07:49:01 UTC  
> Url: https://github.com/boostorg/beast/issues/1142  

### Version of Beast  
1.67  
  
### Steps necessary to reproduce the problem  
I modified the [http_client_async_ssl.cpp](https://www.boost.org/doc/libs/develop/libs/beast/example/http/client/async-ssl/http_client_async_ssl.cpp) example by adding one line  
```  
std::thread t([&ioc] { ioc.run(); });  
```  
above the  
```  
ioc.run();  
```  
in `main()`, and `#include <thread>`. My understanding is that the example is already serialized (i.e. only one async operation is active at any time), so no extra synchronization/strands should be required.  
  
However, TSan reports data race within boost::asio::detail::conditionally_enabled_mutex:  
```  
WARNING: ThreadSanitizer: data race (pid=76741)  
  Read of size 1 at 0x7b280000a0f8 by main thread:  
    #0 boost::asio::detail::conditionally_enabled_mutex::scoped_lock::scoped_lock(boost::asio::detail::conditionally_enabled_mutex&) <null> (trader:x86_64+0x10000d494)  
    #1 boost::asio::detail::conditionally_enabled_mutex::scoped_lock::scoped_lock(boost::asio::detail::conditionally_enabled_mutex&) <null> (trader:x86_64+0x10000d268)  
    #2 boost::asio::detail::kqueue_reactor::run(long, boost::asio::detail::op_queue<boost::asio::detail::scheduler_operation>&) <null> (trader:x86_64+0x1000c3056)  
    #3 boost::asio::detail::scheduler::do_run_one(boost::asio::detail::conditionally_enabled_mutex::scoped_lock&, boost::asio::detail::scheduler_thread_info&, boost::system::error_code const&) <null> (trader:x86_64+0x1000c266c)  
    #4 boost::asio::detail::scheduler::run(boost::system::error_code&) <null> (trader:x86_64+0x1000c221a)  
    #5 boost::asio::io_context::run() <null> (trader:x86_64+0x100004608)  
    #6 main <null> (trader:x86_64+0x100003a41)  
```  
```  
  Previous write of size 1 at 0x7b280000a0f8 by thread T2 (mutexes: write M184):  
    #0 boost::asio::detail::conditionally_enabled_mutex::conditionally_enabled_mutex(bool) <null> (trader:x86_64+0x10000c7b8)  
    #1 boost::asio::detail::conditionally_enabled_mutex::conditionally_enabled_mutex(bool) <null> (trader:x86_64+0x10000c24d)  
    #2 boost::asio::detail::kqueue_reactor::descriptor_state::descriptor_state(bool) <null> (trader:x86_64+0x100039992)  
    #3 boost::asio::detail::kqueue_reactor::descriptor_state::descriptor_state(bool) <null> (trader:x86_64+0x10003990d)  
    #4 boost::asio::detail::kqueue_reactor::descriptor_state* boost::asio::detail::object_pool_access::create<boost::asio::detail::kqueue_reactor::descriptor_state, bool>(bool) <null> (trader:x86_64+0x10003986d)  
(more lines omitted)  
```  
  
### All relevant compiler information  
MacOS, Apple LLVM 9.1.0  
I compiled the program using Bazel; the boost library is built using [this](https://github.com/nelhage/rules_boost/blob/master/BUILD.boost) BUILD rule. I added an extra option `--fsanitize=thread` to enable thread sanitizer.

---

## Comment 1

> Username: djarek  
> Created at: 2018-05-26 13:24:22 UTC  
> Url: https://github.com/boostorg/beast/issues/1142#issuecomment-392261278  

This looks like an issue with Boost.ASIO, rather than Beast.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2018-05-26 13:27:05 UTC  
> Url: https://github.com/boostorg/beast/issues/1142#issuecomment-392261446  

> My understanding is that the example is already serialized (i.e. only one async operation is active at any time)  
  
Nope! You're using `ssl::stream`. Asynchronous SSL reads cause both reads and writes. And these operations are pending simultaneously. You must use an explicit strand using `ssl::stream` when there is more than one thread calling `io_service::run`, even if your higher level protocol (HTTP in this case) is half-duplex.

---

## Comment 3

> Username: kirakirapower  
> Created at: 2018-05-26 18:18:08 UTC  
> Updated at: 2018-05-26 18:18:41 UTC  
> Url: https://github.com/boostorg/beast/issues/1142#issuecomment-392278709  

Hi Vinnie, thanks for the reply. Two follow-up questions:  
  
1.  
> You must use an explicit strand using `ssl::stream` when there is more than one thread calling `io_service::run`  
  
I apologise if my question is dumb, but how do I achieve this? I know I can bind completion handlers to an explicit strand, but as far as I understand, it only guantees that completion handlers are called sequentially, but does not prevent concurrent workers from picking up work issued by the internals of `ssl::stream` (if that's what you mean). Is there an example to follow, or any documentation I have missed?  
  
2. I tried the non-SSL version example as well and the issue still exists.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2018-05-26 18:30:30 UTC  
> Url: https://github.com/boostorg/beast/issues/1142#issuecomment-392279417  

>  it only guantees that completion handlers are called sequentially, but does not prevent concurrent workers from picking up work issued by the internals of `ssl::stream` (if that's what you mean)  
  
Yes that is what I mean and yes it does provide stronger guarantees than what you stated:  
  
_"When asynchronous operations are composed from other asynchronous operations, all intermediate handlers should be invoked using the same method as the final handler."_  
  
Asio provides two mechanisms to accomplish this: the _associated executor_ (for net.ts-flavored Asio versions 1.66 and later) and `boost::asio::asio_handler_invoke` (for earlier versions):  
  
The strand objects provided by Asio, as well as the implementations for composed operations, all specialize these hooks (as does Beast):  
https://www.boost.org/doc/libs/1_67_0/doc/html/boost_asio/reference/asio_handler_invoke.html  
  
To make sure that I/O objects are not accessed in ways that violate their preconditions in a multi-threaded `io_context` scenario, wrap your completion handlers in a strand.  
  
> I tried the non-SSL version example as well and the issue still exists.  
  
No idea about that. Can you dig a little deeper and get more info?

---

## Comment 5

> Username: kirakirapower  
> Created at: 2018-05-26 22:02:14 UTC  
> Url: https://github.com/boostorg/beast/issues/1142#issuecomment-392290840  

> _"When asynchronous operations are composed from other asynchronous operations, all intermediate handlers should be invoked using the same method as the final handler."_  
  
Thanks for the info! I certainly missed that sentence.  
  
You earlier said asynchronous SSL reads can cause both pending reads and writes on the underlying socket. Follow-up questions on that:  
1. Can I have an async SSL read and an async SSL write pending at the same time?  
2. Suppose the answer to (1) is yes, do their completion handlers need to be wrapped in the same strand, assuming there are multiple threads running the io_context and the completion handlers do not access any shared objects?  
3. How about `ssl::stream::async_shutdown`? Can I call `async_shutdown` while there is a pending `async_read` or/and a pending `async_write`?  
  
Such characteristics for `beast::websocket::stream` is clearly [documented](https://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/using_websocket/notes.html#beast.using_websocket.notes.thread_safety) (thanks!), but I could not find similar information for `ssl::stream` or `beast::http::async_{read,write}`.  
  
> No idea about that. Can you dig a little deeper and get more info?  
  
Here is a minimal reproduce, which only calls async_resolve and async_connect. ThreadSanitizer's output is the same as the original one. Do you see any issues with my code? If not, it's either an asio bug or a compiler bug.  
```  
#include <boost/asio.hpp>  
#include <functional>  
#include <thread>  
  
using tcp = boost::asio::ip::tcp;  
  
class session {  
public:  
  explicit session(boost::asio::io_context &ioc)  
      : strand_(ioc), resolver_(ioc), socket_(ioc) {}  
  
  void run(char const *host, char const *port) {  
    resolver_.async_resolve(  
        host, port,  
        boost::asio::bind_executor(  
            strand_, std::bind(&session::on_resolve, this,  
                               std::placeholders::_1, std::placeholders::_2)));  
  }  
  
private:  
  void on_resolve(boost::system::error_code ec,  
                  tcp::resolver::results_type results) {  
    if (ec)  
      return;  
  
    boost::asio::async_connect(  
        socket_, results.begin(), results.end(),  
        boost::asio::bind_executor(  
            strand_,  
            std::bind(&session::on_connect, this, std::placeholders::_1)));  
  }  
  
  void on_connect(boost::system::error_code) {}  
  
  boost::asio::io_context::strand strand_;  
  tcp::resolver resolver_;  
  tcp::socket socket_;  
};  
  
int main(int argc, char **argv) {  
  boost::asio::io_context ioc;  
  session s(ioc);  
  s.run("www.github.com", "80");  
  
  std::thread t([&ioc] { ioc.run(); });  
  ioc.run();  
  t.join();  
  return EXIT_SUCCESS;  
}  
```

---

## Comment 6

> Username: vinniefalco  
> Created at: 2018-05-26 22:41:07 UTC  
> Updated at: 2018-05-26 22:42:56 UTC  
> Url: https://github.com/boostorg/beast/issues/1142#issuecomment-392292495  

To be honest at this point I am not as sure how `ssl::stream` works as I was this morning. I just read this:  
https://stackoverflow.com/questions/33041635/boost-asio-ssl-how-do-strands-help-the-implementation  
But there is no `strand_` in the implementation. I have to study Asio's implementation a bit more to get a better understanding of how it works. I will try answer your questions but they might not be completely right:  
  
> Can I have an async SSL read and an async SSL write pending at the same time?  
  
Definitely yes.  
  
> Suppose the answer to (1) is yes, do their completion handlers need to be wrapped in the same strand, assuming there are multiple threads running the io_context and the completion handlers do not access any shared objects?  
  
That's a good question and one I am unsure about. If the Asio ssl implementation has a strand-equivalent internally then the answer would be no (assuming no access to shared objects in the completion handler, including the `ssl::stream` and underlying socket).  
  
If there is no strand in the implementation, then it depends on whether the ssl engine is full-duplex or half-duplex. If it never tries to perform simultaneous reads and writes (half-duplex) then you don't need a strand. Otherwise yes.  
  
> How about ssl::stream::async_shutdown? Can I call async_shutdown while there is a pending async_read or/and a pending async_write?  
  
Yes you can call `async_shutdown` at any time, as long as you do it from the same implicit or explicit strand.  
  
> I could not find similar information for ssl::stream or beast::http::async_{read,write}.  
  
I agree, the documentation for `ssl::stream` does not make this clear. As for Beast HTTP it depends on the underlying stream that you are reading or writing to. For regular sockets, no strand needed. For `ssl::stream`, it all depends on the answers to your questions above. Note that Beast documentation does in fact address this: `beast::http::async_read` states _"This operation is implemented in terms of one or more calls to the stream's async_read_some function, and is known as a composed operation. The program must ensure that the stream performs no other reads until this operation completes."_. You have to account for the stream's behavior to get the full picture.

---

## Comment 7

> Username: vinniefalco  
> Created at: 2018-05-26 22:42:31 UTC  
> Url: https://github.com/boostorg/beast/issues/1142#issuecomment-392292543  

Based on your MVCE, I believe that ThreadSanitizer is generating a false positive.

---

## Comment 8

> Username: kirakirapower  
> Created at: 2018-06-06 07:51:12 UTC  
> Url: https://github.com/boostorg/beast/issues/1142#issuecomment-394975360  

>> How about ssl::stream::async_shutdown? Can I call async_shutdown while there is a pending async_read or/and a pending async_write?  
  
> Yes you can call async_shutdown at any time, as long as you do it from the same implicit or explicit strand.  
  
Slightly off-topic but I recently encountered an issue with this. The `async_shutdown` handler never completes when there is an `async_read` ongoing. This only happens under Linux, not MacOS. I open an asio [ticket](https://svn.boost.org/trac10/ticket/13587) if you are interested.

---

## Comment 9

> Username: vinniefalco  
> Created at: 2018-06-06 11:16:00 UTC  
> Url: https://github.com/boostorg/beast/issues/1142#issuecomment-395032923  

>  I open an asio ticket if you are interested.  
  
Yes you should do that

---

## Comment 10

> Username: TechnikEmpire  
> Created at: 2018-06-09 06:20:54 UTC  
> Updated at: 2018-06-09 06:46:47 UTC  
> Url: https://github.com/boostorg/beast/issues/1142#issuecomment-395944141  

Asio's SSL stream and friends are an extremely thin but tightly bound "abstraction" around raw openssl structures. So no, it is not doing you any favours and it's basically like using raw openSSL but with some sweet c++ syntax sprinkled around.  
  
About the only favour asio is doing for you in this department is automatically calling the openSSL synchronization calls. openSSL enables optional thread locking built-in when configured (for ops on structs such as SSL_CTX and friends that asio wrap for you). Asio uses a neat static initializer to configure this global locking system on startup. On Windows, a similar initializer calls wsastartup and shutdown to init/shutdown the required winsock subsystem. This is also the reason why, when you build asio into your own shared library, you need to manually include asio source files and then manually call this static initializer, because it doesn't happen for you automatically anymore from a shared library. I can't recall if the same applies to the openSSL initializer, but it may. Just for completeness.  
  
Anyway I'm getting off topic. Aside from auto-configuring the openSSL thread lock system when directly compiled into your executable, asio is not helping you in any way with openSSL. So as far as strands go, and especially encrypted streams, the rule is one strand per stream (implict or explicit). That includes both reads and writes.  
  
Don't screw this up. Doing asynchronous operations on an encrypted stream without using a strand to force ordered/synchronized, chained handlers via a strand will lead to a plethora of random and often nonsensical failures and when you try to read the error information out of openSSL or asio (which just pulls from openSSL), the errors and their messages will be inconsistent and randomly irrelevant sending you on a wild goose chase.

---

## Comment 11

> Username: stale[bot]  
> Created at: 2018-07-09 07:22:28 UTC  
> Url: https://github.com/boostorg/beast/issues/1142#issuecomment-403384118  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 12

> Username: stale[bot]  
> Created at: 2018-07-16 07:48:59 UTC  
> Url: https://github.com/boostorg/beast/issues/1142#issuecomment-405170907  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!

---

## Comment 13

> Username: devgs  
> Created at: 2019-11-07 10:42:02 UTC  
> Updated at: 2019-11-07 10:43:51 UTC  
> Url: https://github.com/boostorg/beast/issues/1142#issuecomment-551024295  

If anyone is still interested. This is definitely an issue in `asio` that we have to fix ourselves. Strands could not possibly help here, since it's an internal `ssl::stream` implementation bug. The issue is in a concurrent access to `asio:ssl::detail::stream_core` by multiple `asio:ssl::detail::io_op`. `stream_core` has no means in place to serialise access, so this happens easily on `io_context`'s that are being served by more that one thread, when you issue both `async_read_some` and `async_write_some` on the same socket.  
  
This is inevitable in async protocols, when you have to both send and receive data from peer, for example: HTTP/2.  
  
The fix is pretty easy:  
  
Add `recursive_mutex` to `asio:ssl::detail::stream_core` and then lock it at the start of `operator()` of `io_ip`. Non-recursive mutex won't work, because `io_ip` may call itself recursively.

---

## Comment 14

> Username: vinniefalco  
> Created at: 2019-11-07 11:45:45 UTC  
> Url: https://github.com/boostorg/beast/issues/1142#issuecomment-551045800  

What makes you think a `strand` doesn't help?

---

## Comment 15

> Username: devgs  
> Created at: 2019-11-07 11:55:33 UTC  
> Updated at: 2019-11-07 11:56:59 UTC  
> Url: https://github.com/boostorg/beast/issues/1142#issuecomment-551049179  

@vinniefalco, please, elaborate, how does strand helps here? Strand only serialises execution of your handlers. `ssl::stream` knows nothing about your strands, and it shouldn't. Maybe you though that i meant that `async_read_some` and `async_write_some` are issued concurrently? No, no, they are issues from within the same strand, in sequence. But, since requests are async in nature, corresponding `io_op`s may actually be executed concurrently, later when the underlying socket supplies some data. That's because, `async_read_some` and `async_write_some` on the `lowest_layer()`(!) are issued without a strand, and they are issued internally, not by you, even though it's the consequence of your call to higher-level API of `ssl::stream`.

---

## Comment 16

> Username: vinniefalco  
> Created at: 2019-11-07 12:04:24 UTC  
> Updated at: 2019-11-07 12:04:37 UTC  
> Url: https://github.com/boostorg/beast/issues/1142#issuecomment-551051995  

> Strand only serialises execution of your handlers. ssl::stream knows nothing about your strands, and it shouldn't.  
  
This is incorrect. When an asynchronous initiating function launches a composed operation, the intermediate asynchronous operations use the same executor as the executor associated with the handler. This means that when you submit a completion handler which is wrapped in a `strand`, all intermediate operations will use the strand. Otherwise, it would have the problem you described.  
  
The whole point of executors is to support the "concurrency without explicit locking" described here:  
https://www.boost.org/doc/libs/1_71_0/doc/html/boost_asio/overview/core/strands.html  
  
A `recursive_mutex`  is not the right thing to do here.  
  
You can verify this behavior yourself, note that the SSL implementation overloads `asio_handler_invoke` for its composed operation, and the definition for this overload calls the overload of `asio_handler_invoke` which is associated with the handler (in this case a `strand`):  
https://github.com/boostorg/asio/blob/develop/include/boost/asio/ssl/detail/io.hpp#L325  
  
I'm certain that you're doing something wrong.

---

## Comment 17

> Username: devgs  
> Created at: 2019-11-07 13:24:06 UTC  
> Url: https://github.com/boostorg/beast/issues/1142#issuecomment-551077170  

@vinniefalco   
Please, look carefully at these lines:  
https://github.com/boostorg/asio/blob/develop/include/boost/asio/ssl/detail/io.hpp#L167  
https://github.com/boostorg/asio/blob/develop/include/boost/asio/ssl/detail/io.hpp#L194  
https://github.com/boostorg/asio/blob/develop/include/boost/asio/ssl/detail/io.hpp#L217  
  
Those are the sources of the problem I describe, there're no composed handlers. `io_op` uses itself as a handler, without any references to executors.

---

## Comment 18

> Username: vinniefalco  
> Created at: 2019-11-07 13:55:04 UTC  
> Url: https://github.com/boostorg/beast/issues/1142#issuecomment-551088863  

>  there're no composed handlers. io_op uses itself as a handler, without any references to executors.  
  
Do you fully understand this line?  
https://github.com/boostorg/asio/blob/b6c27b9c3167b9b7dd0a30de9dfe6f3aa33bdaf3/include/boost/asio/ssl/detail/io.hpp#L331  
  
See:  
https://github.com/boostorg/asio/blob/b6c27b9c3167b9b7dd0a30de9dfe6f3aa33bdaf3/include/boost/asio/ssl/detail/io.hpp#L331  
  
Note that there are **many** individuals and companies using `asio::ssl::stream` in multi-threaded I/IO contexts and strands just fine. Observe that there are few to no issues about using `ssl::stream` with beast, in multi-threaded contexts. In the few issues that exist, the problem is always with the author's code. The problem is in your implementation, I am certain of it.

---

## Comment 19

> Username: devgs  
> Created at: 2019-11-07 14:02:08 UTC  
> Url: https://github.com/boostorg/beast/issues/1142#issuecomment-551091757  

@vinniefalco   
Does **beast** have scenarios, when it issues reads and writes simultaneously, not one after another, on the same stream?

---

## Comment 20

> Username: vinniefalco  
> Created at: 2019-11-07 14:04:02 UTC  
> Url: https://github.com/boostorg/beast/issues/1142#issuecomment-551092530  

For HTTP no, for websocket yes.

---

## Comment 21

> Username: geraldagapov  
> Created at: 2019-12-28 12:31:36 UTC  
> Url: https://github.com/boostorg/beast/issues/1142#issuecomment-569413084  

@vinniefalco Thanks a lot for your comments here, I should say my understanding on boost asio have significantly improved :)   
  
I still don't understand how to perform async_read and sync write on the same ssl::stream. I guess I can try to mock sync write with async_write plus future.wait(). Although I feel like I'm doing something wrong.

---

## Comment 22

> Username: vinniefalco  
> Created at: 2019-12-28 16:04:36 UTC  
> Url: https://github.com/boostorg/beast/issues/1142#issuecomment-569428541  

> I still don't understand how to perform async_read and sync write on the same ssl::stream  
  
You don't. Mixing synchronous and asynchronous calls produces undefined behavior.

---

## Comment 23

> Username: geraldagapov  
> Created at: 2019-12-28 17:29:34 UTC  
> Updated at: 2019-12-28 18:34:24 UTC  
> Url: https://github.com/boostorg/beast/issues/1142#issuecomment-569436212  

Do you mean that calls to sync and async APIs has to be syncronized or that using then on the same object is not allowed at all?  
  
Or you mean that the approach that I described (with waiting on for async_write to complete) also is prone to undefined behavior?

---

## Comment 24

> Username: madmongo1  
> Created at: 2019-12-28 20:30:40 UTC  
> Url: https://github.com/boostorg/beast/issues/1142#issuecomment-569448897  

You shouldn’t call a sync method while there is an a sync method awaiting  
completion.  
  
Similarly, initiating an async method on another thread while there is a  
sync method in progress would be UB.  
  
  
On Sat, 28 Dec 2019 at 18:29, Gerald Agapov <notifications@github.com>  
wrote:  
  
> Do you mean that calls to sync and async APIs has to be syncronized or  
> that using then on the same object is not allowed at all?  
>  
> —  
> You are receiving this because you are subscribed to this thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/beast/issues/1142?email_source=notifications&email_token=ABHOZSKZECQFYWJ66K4TC2DQ26EIHA5CNFSM4FB3OEEKYY3PNVWWK3TUL52HS4DFVREXG43VMVBW63LNMVXHJKTDN5WW2ZLOORPWSZGOEHYOQNA#issuecomment-569436212>,  
> or unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/ABHOZSP444EKODLFGEIYCBDQ26EIHANCNFSM4FB3OEEA>  
> .  
>  
--   
Richard Hodges  
hodges.r@gmail.com  
office: +442032898513  
home: +376841522  
mobile: +376380212

---

## Comment 25

> Username: vinniefalco  
> Created at: 2019-12-28 21:01:32 UTC  
> Url: https://github.com/boostorg/beast/issues/1142#issuecomment-569450834  

> You shouldn’t call a sync method while there is an a sync method awaiting completion.  
  
More generally "I/O objects *are not thread safe*." How could you call another sync method if you are waiting on one already? Answer: from another thread, which is undefined (because I/O objects *are not thread safe*).

---

## Comment 26

> Username: geraldagapov  
> Created at: 2019-12-28 21:40:28 UTC  
> Url: https://github.com/boostorg/beast/issues/1142#issuecomment-569453166  

Ok, now I see what you mean. What I was trying to do is to run a sync write from within an async_read completion handler, which should not require synchronization as it is done from the same thread.

---

## Comment 27

> Username: vinniefalco  
> Created at: 2019-12-29 00:39:49 UTC  
> Url: https://github.com/boostorg/beast/issues/1142#issuecomment-569463139  

> What I was trying to do is to run a sync write from within an async_read  
  
Never in my career have I seen any good come from this.
