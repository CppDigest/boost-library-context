# #1418 - test::stream doesn't keep an executor work guard? [Closed]

> Username: reddwarf69  
> Created at: 2019-01-28 15:20:28 UTC  
> Updated at: 2019-02-03 20:59:32 UTC  
> Closed at: 2019-02-03 20:59:32 UTC  
> Url: https://github.com/boostorg/beast/issues/1418  

So this is likely to be a misunderstanding on my part. But just in case I have found an actual bug...  
  
When running this code with Boost 1.69  
  
```  
#include <boost/asio.hpp>  
#include <boost/beast.hpp>  
#include <boost/beast/experimental/test/stream.hpp>  
#include <iostream>  
  
int main() {  
  boost::asio::io_context client_ioc;  
  boost::asio::io_context server_ioc;  
  
  boost::beast::websocket::stream<boost::beast::test::stream> client_stream(  
      client_ioc);  
  boost::beast::websocket::stream<boost::beast::test::stream> server_stream(  
      server_ioc);  
  
  client_stream.lowest_layer().connect(server_stream.lowest_layer());  
  
  client_stream.async_handshake("", "no_empty",  
                                [](const boost::beast::error_code &) {});  
  server_stream.async_accept([](const boost::beast::error_code &) {});  
  
  client_ioc.poll();  
  server_ioc.poll();  
  client_ioc.poll();  
  
  client_ioc.restart();  
  server_ioc.restart();  
  
  // THE WEBSOCKET SOCKET IS NOW CONNECTED  
  
  boost::asio::io_context client_handler_ioc;  
  
  boost::beast::flat_buffer buffer;  
  
  client_stream.async_write(  
      boost::asio::buffer("Can I connect?"),  
      boost::asio::bind_executor(  
          client_handler_ioc,  
          [&client_stream, &buffer,  
           &client_handler_ioc](const boost::beast::error_code &, std::size_t) {  
            std::clog << "LALA0" << std::endl;  
            client_stream.async_read(  
                buffer, boost::asio::bind_executor(  
                            client_handler_ioc,  
                            [](const boost::beast::error_code &, std::size_t) {  
                              std::clog << "LALA1" << std::endl;  
                            }));  
          }));  
  
  client_ioc.poll();  
  client_handler_ioc.poll();  
  
  std::clog << client_ioc.stopped() << std::endl;  
  std::clog << client_handler_ioc.stopped() << std::endl;  
}  
```  
  
I get this output:  
```  
LALA0  
0  
1  
```  
  
It's my understanding that after calling `client_handler_ioc.poll()` `client_stream.async_read(buffer, boost::asio::bind_executor(client_handler_ioc, <completion_handler>));` has been called. I would have expected that call to async_read to have created a "work" for the client_handler_ioc executor, and so it should have not "stopped".

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-01-28 15:31:51 UTC  
> Url: https://github.com/boostorg/beast/issues/1418#issuecomment-458176992  

This looks like a bug!

---

## Comment 2

> Username: vinniefalco  
> Created at: 2019-01-28 17:15:41 UTC  
> Url: https://github.com/boostorg/beast/issues/1418#issuecomment-458219650  

Are we sure this isn't a problem with `test::stream` (rather than `websocket::stream`)?

---

## Comment 3

> Username: reddwarf69  
> Created at: 2019-01-28 17:22:46 UTC  
> Url: https://github.com/boostorg/beast/issues/1418#issuecomment-458222251  

Not really sure, no.  
  
Since I was not sure it's a real issue I have not explored it in too much detail yet. I found the issue testing an implementation of a real protocol over Websockets.  
When trying to create a simple reproducible example test::stream was my first suspect, but I moved to something with Websockets when this worked fine:  
  
Using this  
```  
int main(int argc, char** argv)  
{  
    using socket_type = boost::beast::test::stream;  
      
    boost::asio::io_context ioc;  
    socket_type client_sock{ioc};  
    socket_type server_sock{ioc};  
      
    client_sock.connect(server_sock);  
      
    write(client_sock, boost::asio::buffer("TEST\n"));  
  
    boost::asio::io_context handler_ioc;      
    async_echo(server_sock, boost::asio::bind_executor(handler_ioc,  
        [&](boost::beast::error_code ec)  
        {  
            std::clog << "EXECUTOR: " << ioc.get_executor().running_in_this_thread() << " " << handler_ioc.get_executor().running_in_this_thread() << std::endl;  
            std::clog << ec.message() << std::endl;  
        }));  
      
    ioc.poll();  
    std::clog << "A: " << ioc.stopped() << " " << handler_ioc.stopped() << std::endl;  
    handler_ioc.poll();  
    std::clog << "B: " << ioc.stopped() << " " << handler_ioc.stopped() << std::endl;  
    ioc.poll();  
    std::clog << "C: " << ioc.stopped() << " " << handler_ioc.stopped() << std::endl;  
    handler_ioc.poll();  
    std::clog << "D: " << ioc.stopped() << " " << handler_ioc.stopped() << std::endl;  
}  
```  
  
to "test" https://www.boost.org/doc/libs/1_69_0/libs/beast/example/echo-op/echo_op.cpp I get  
  
```  
$ ./echo_op   
A: 0 0  
B: 0 0  
C: 0 0  
EXECUTOR: 0 1  
Success  
D: 1 1  
$  
```  
  
Which looks fine... but it's not exactly the same.

---

## Comment 4

> Username: djarek  
> Created at: 2019-01-28 17:34:25 UTC  
> Updated at: 2019-01-28 17:34:57 UTC  
> Url: https://github.com/boostorg/beast/issues/1418#issuecomment-458226571  

The behavior of Beast is correct - asynchronous operations cannot maintain I/O work guard for you, so if your handler context is different than your I/O context, you need to maintain a work guard yourself.  
  
(If the same context is used for both, you can safely omit the work guard).

---

## Comment 5

> Username: reddwarf69  
> Created at: 2019-01-28 18:05:26 UTC  
> Url: https://github.com/boostorg/beast/issues/1418#issuecomment-458238112  

Isn't https://www.boost.org/doc/libs/1_69_0/doc/html/boost_asio/reference/asynchronous_operations.html  
  
```  
* Associated completion handler executor  
  
A completion handler object of type CompletionHandler has an associated executor of type Executor2 satisfying the Executor requirements. The type Executor2 is associated_executor_t<CompletionHandler, Executor1>. Let ex2 be a value of type Executor2 obtained by performing get_associated_executor(completion_handler, ex1).  
  
* Outstanding work  
  
Until the asynchronous operation has completed, the asynchronous operation shall maintain:  
  
— an object work1 of type executor_work_guard<Executor1>, initialized as work1(ex1), and where work1.owns_work() == true; and  
  
— an object work2 of type executor_work_guard<Executor2>, initialized as work2(ex2), and where work2.owns_work() == true.   
```  
  
saying asynchronous operations will maintain I/O work guard for me?

---

## Comment 6

> Username: vinniefalco  
> Created at: 2019-01-28 18:40:19 UTC  
> Url: https://github.com/boostorg/beast/issues/1418#issuecomment-458250821  

> saying asynchronous operations will maintain I/O work guard for me?  
  
Yes, but **your** asynchronous operation as written does not maintain a guard across multiple calls to **Beast's** asynchronous operations. In your first code example, there is an implicit composed asynchronous operation, the `async_write` followed by the `async_read`. There is a moment after the async_write completes, but before the async_read begins where you have no guard. Beast can't put one there for you (although Damian is proposing that be changed) so you need to add it yourself.

---

## Comment 7

> Username: djarek  
> Created at: 2019-01-28 19:05:46 UTC  
> Url: https://github.com/boostorg/beast/issues/1418#issuecomment-458259907  

@RedDwarf69 You have to keep in mind what completion, in the context of async operations, means:  
>If an asynchonous operation completes immediately (that is, within the thread of execution calling the initiating function, and before the initiating function returns), the completion handler shall be submitted for execution as if by performing ex2.post(std::move(f), alloc2). Otherwise, the completion handler shall be submitted for execution as if by performing ex2.dispatch(std::move(f), alloc2).   
  
So work guards have to be maintained until completion, and completion means `ex2.dispatch()`. The I/O work guard has to be released before the call to `dispatch` and the `ex2` guard after the call to `dispatch`.

---

## Comment 8

> Username: reddwarf69  
> Created at: 2019-01-28 21:39:33 UTC  
> Url: https://github.com/boostorg/beast/issues/1418#issuecomment-458312048  

I see now that while simplifying I forgot about the AsyncStream executor work guard, is this the one you are talking about? Notice that the problem I'm seeing is related to the handler executor work guard.  
  
To play with something more familiar. Let's look at this code, a modified version of echo_op:  
  
```  
#include <boost/asio.hpp>  
#include <boost/beast.hpp>  
#include <boost/beast/experimental/test/stream.hpp>  
#include <cstddef>  
#include <iostream>  
#include <memory>  
#include <utility>  
  
static boost::beast::flat_buffer buffer;  
  
template <class AsyncMessageStream, class Handler> class connect_op {  
  struct state {  
    AsyncMessageStream &stream;  
  
    boost::asio::executor_work_guard<decltype(  
        std::declval<AsyncMessageStream &>().get_executor())>  
        work;  
  
    int step = 0;  
  
    explicit state(Handler const &handler, AsyncMessageStream &stream_)  
        : stream(stream_), work(stream.get_executor()) {}  
  };  
  
  boost::beast::handler_ptr<state, Handler> p_;  
  
public:  
  connect_op(connect_op &&) = default;  
  
  template <class DeducedHandler, class... Args>  
  connect_op(AsyncMessageStream &stream, DeducedHandler &&handler)  
      : p_(std::forward<DeducedHandler>(handler), stream) {}  
  
  using allocator_type = boost::asio::associated_allocator_t<Handler>;  
  
  allocator_type get_allocator() const noexcept {  
    return (boost::asio::get_associated_allocator)(p_.handler());  
  }  
  
  using executor_type = boost::asio::associated_executor_t<  
      Handler, decltype(std::declval<AsyncMessageStream &>().get_executor())>;  
  
  executor_type get_executor() const noexcept {  
    return (boost::asio::get_associated_executor)(p_.handler(),  
                                                  p_->stream.get_executor());  
  }  
  
  void operator()(boost::beast::error_code ec, std::size_t bytes_transferred);  
};  
  
template <class AsyncMessageStream, class Handler>  
void connect_op<AsyncMessageStream, Handler>::  
operator()(boost::beast::error_code ec, std::size_t bytes_transferred) {  
  auto &p = *p_;  
  
  switch (ec ? 2 : p.step) {  
  case 0:  
    p.step = 1;  
    return p.stream.async_write(boost::asio::buffer("Can I connect?"),  
                                std::move(*this));  
  
  case 1:  
    p.step = 2;  
    return p.stream.async_read(buffer, std::move(*this));  
  
  case 2:  
    buffer.consume(bytes_transferred);  
    break;  
  }  
  
  auto work = std::move(p.work);  
  p_.invoke(ec);  
}  
  
template <class AsyncMessageStream, class CompletionToken>  
BOOST_ASIO_INITFN_RESULT_TYPE(CompletionToken, void(boost::beast::error_code))  
async_connect(AsyncMessageStream &stream, CompletionToken &&token) {  
  boost::asio::async_completion<CompletionToken, void(boost::beast::error_code)>  
      init{token};  
  
  connect_op<AsyncMessageStream,  
             BOOST_ASIO_HANDLER_TYPE(CompletionToken,  
                                     void(boost::beast::error_code))>{  
      stream, init.completion_handler}(boost::beast::error_code{}, 0);  
  
  return init.result.get();  
}  
  
int main() {  
  boost::asio::io_context client_ioc;  
  boost::asio::io_context server_ioc;  
  boost::beast::websocket::stream<boost::beast::test::stream> client_sock{  
      client_ioc};  
  boost::beast::websocket::stream<boost::beast::test::stream> server_sock{  
      server_ioc};  
  
  client_sock.lowest_layer().connect(server_sock.lowest_layer());  
  
  client_sock.async_handshake("", "no_empty",  
                              [](const boost::beast::error_code &) {});  
  server_sock.async_accept([](const boost::beast::error_code &) {});  
  
  client_ioc.poll();  
  server_ioc.poll();  
  client_ioc.poll();  
  
  client_ioc.restart();  
  server_ioc.restart();  
  
  boost::asio::io_context handler_ioc;  
  async_connect(  
      client_sock,  
      boost::asio::bind_executor(handler_ioc, [&](boost::beast::error_code ec) {  
        std::clog << "EXECUTOR: "  
                  << client_ioc.get_executor().running_in_this_thread() << " "  
                  << handler_ioc.get_executor().running_in_this_thread()  
                  << std::endl;  
        std::clog << ec.message() << std::endl;  
      }));  
  
  client_ioc.poll();  
  handler_ioc.poll();  
  std::clog << "AsyncMessageStream executor stopped: " << client_ioc.stopped()  
            << "\nHandler executor stopped: " << handler_ioc.stopped()  
            << std::endl;  
  
  // If the following line is removed it doesn't work  
  handler_ioc.restart();  
  
  server_sock.write(boost::asio::buffer("Yes"));  
  
  client_ioc.poll();  
  handler_ioc.poll();  
}  
```  
  
(I made the flat_buffer global to keep it simple)  
This example shows the same problem. The output is:  
  
```  
AsyncMessageStream executor stopped: 0  
Handler executor stopped: 1  
EXECUTOR: 0 1  
Success  
```  
  
My asynchronous operation certainly doesn't keep a work guard for the handler associated executor. But neither does the echo_op example from Beast.  
  
Are you arguing the echo_op example should keep a work guard for the handler executor because between https://github.com/boostorg/beast/blob/boost-1.69.0/example/echo-op/echo_op.cpp#L212 and https://github.com/boostorg/beast/blob/boost-1.69.0/example/echo-op/echo_op.cpp#L219 there is a moment where there is no guard?

---

## Comment 9

> Username: vinniefalco  
> Created at: 2019-01-28 21:48:06 UTC  
> Url: https://github.com/boostorg/beast/issues/1418#issuecomment-458314719  

> Are you arguing the echo_op example should keep a work guard for the handler executor   
  
It does look like a guard is missing from echo-op!

---

## Comment 10

> Username: reddwarf69  
> Created at: 2019-01-28 22:27:46 UTC  
> Url: https://github.com/boostorg/beast/issues/1418#issuecomment-458327499  

FWIW I was asking myself why the echo_op is as it is and in the boost-users mailing list Damian managed to convince me the second guard is not needed (https://lists.boost.org/boost-users/2018/11/89019.php).  
  
Basically, I would expect this in my latest example:  
  
* async_connect() is called  
stream.async_write() is called, which will end up calling something creating a work guard for the handler executor. So async_connect() doesn't need to create its own guard for the handler executor.  
-- handler executor work count: 1 --  
  
* client_ioc.poll() is called  
async_write does its work and enqueues the completion handler into handler_ioc. This enqueued completion handler is work, so it increases the work count  
-- handler executor work count: 2 --  
but since async_write() has finished it's not keeping a work guard any more, so it decreases the work count  
-- handler executor work count: 1 --  
  
* handler_ioc.poll() is called  
The completion handler is executed, which ends up calling stream.async_read(). async_read(), as async_write() before, takes care of the work guard for the handler executor.  
-- handler executor work count: 2 --  
Only after async_read() has been called, when the completion handler finishes executing, the work count decreases.  
-- handler executor work count: 1 --  
  
  
So, without async_connect() explicitly having a handler executor work guard, the handler executor work count is never zero.  
  
  
  
Also in the brand new composed operation example from asio only one work guard is explicitly created -> https://github.com/boostorg/asio/blob/develop/example/cpp11/operations/composed_5.cpp#L84

---

## Comment 11

> Username: reddwarf69  
> Created at: 2019-01-29 10:24:41 UTC  
> Updated at: 2019-01-29 10:43:08 UTC  
> Url: https://github.com/boostorg/beast/issues/1418#issuecomment-458487370  

The issue (if there is an issue) would be in `test::stream` after all:  
  
```  
#include <boost/asio.hpp>  
#include <boost/beast.hpp>  
#include <boost/beast/experimental/test/stream.hpp>  
#include <cstddef>  
#include <iostream>  
#include <memory>  
#include <utility>  
  
static boost::beast::flat_buffer buffer;  
  
template <class AsyncMessageStream, class Handler> class connect_op {  
  struct state {  
    AsyncMessageStream &stream;  
  
    boost::asio::executor_work_guard<decltype(  
        std::declval<AsyncMessageStream &>().get_executor())>  
        work;  
  
    int step = 0;  
  
    explicit state(Handler const &handler, AsyncMessageStream &stream_)  
        : stream(stream_), work(stream.get_executor()) {}  
  };  
  
  boost::beast::handler_ptr<state, Handler> p_;  
  
public:  
  connect_op(connect_op &&) = default;  
  
  template <class DeducedHandler, class... Args>  
  connect_op(AsyncMessageStream &stream, DeducedHandler &&handler)  
      : p_(std::forward<DeducedHandler>(handler), stream) {}  
  
  using allocator_type = boost::asio::associated_allocator_t<Handler>;  
  
  allocator_type get_allocator() const noexcept {  
    return (boost::asio::get_associated_allocator)(p_.handler());  
  }  
  
  using executor_type = boost::asio::associated_executor_t<  
      Handler, decltype(std::declval<AsyncMessageStream &>().get_executor())>;  
  
  executor_type get_executor() const noexcept {  
    return (boost::asio::get_associated_executor)(p_.handler(),  
                                                  p_->stream.get_executor());  
  }  
  
  void operator()(boost::beast::error_code ec, std::size_t bytes_transferred);  
};  
  
template <class AsyncMessageStream, class Handler>  
void connect_op<AsyncMessageStream, Handler>::  
operator()(boost::beast::error_code ec, std::size_t bytes_transferred) {  
  auto &p = *p_;  
  
  switch (ec ? 2 : p.step) {  
  case 0:  
    p.step = 1;  
    return p.stream.async_write(boost::asio::buffer("Can I connect?"),  
                                std::move(*this));  
  
  case 1:  
    p.step = 2;  
    return p.stream.async_read(buffer, std::move(*this));  
  
  case 2:  
    buffer.consume(bytes_transferred);  
    break;  
  }  
  
  auto work = std::move(p.work);  
  p_.invoke(ec);  
}  
  
template <class AsyncMessageStream, class CompletionToken>  
BOOST_ASIO_INITFN_RESULT_TYPE(CompletionToken, void(boost::beast::error_code))  
async_connect(AsyncMessageStream &stream, CompletionToken &&token) {  
  boost::asio::async_completion<CompletionToken, void(boost::beast::error_code)>  
      init{token};  
  
  connect_op<AsyncMessageStream,  
             BOOST_ASIO_HANDLER_TYPE(CompletionToken,  
                                     void(boost::beast::error_code))>{  
      stream, init.completion_handler}(boost::beast::error_code{}, 0);  
  
  return init.result.get();  
}  
  
int main() {  
  boost::asio::io_context client_ioc;  
  boost::asio::io_context server_ioc;  
    
#if 0  
  boost::beast::websocket::stream<boost::beast::test::stream> client_sock{  
      client_ioc};  
  boost::beast::websocket::stream<boost::beast::test::stream> server_sock{  
      server_ioc};  
  
  client_sock.lowest_layer().connect(server_sock.lowest_layer());  
#else  
  boost::beast::websocket::stream<boost::asio::ip::tcp::socket> client_sock{  
      client_ioc};  
  boost::beast::websocket::stream<boost::asio::ip::tcp::socket> server_sock{  
      server_ioc};  
        
  boost::asio::ip::tcp::endpoint endpoint(boost::asio::ip::address::from_string("127.0.0.1"), 12345);  
        
  boost::asio::ip::tcp::acceptor acceptor{server_ioc, endpoint};  
  acceptor.async_accept(server_sock.lowest_layer(), [](const boost::beast::error_code &) {});  
  client_sock.lowest_layer().async_connect(endpoint, [](const boost::beast::error_code &) {});  
    
  client_ioc.poll();  
  server_ioc.poll();  
  client_ioc.poll();  
    
  client_ioc.restart();  
  server_ioc.restart();  
#endif  
  
  client_sock.async_handshake("", "no_empty",  
                              [](const boost::beast::error_code &) {});  
  server_sock.async_accept([](const boost::beast::error_code &) {});  
  
  client_ioc.poll();  
  server_ioc.poll();  
  client_ioc.poll();  
  
  client_ioc.restart();  
  server_ioc.restart();  
  
  boost::asio::io_context handler_ioc;  
  async_connect(  
      client_sock,  
      boost::asio::bind_executor(handler_ioc, [&](boost::beast::error_code ec) {  
        std::clog << "EXECUTOR: "  
                  << client_ioc.get_executor().running_in_this_thread() << " "  
                  << handler_ioc.get_executor().running_in_this_thread()  
                  << std::endl;  
        std::clog << ec.message() << std::endl;  
      }));  
  
  client_ioc.poll();  
  handler_ioc.poll();  
  std::clog << "AsyncMessageStream executor stopped: " << client_ioc.stopped()  
            << "\nHandler executor stopped: " << handler_ioc.stopped()  
            << std::endl;  
  
  // If the following line is removed it doesn't work  
  handler_ioc.restart();  
  
  server_sock.write(boost::asio::buffer("Yes"));  
  
  client_ioc.poll();  
  handler_ioc.poll();  
}  
```  
  
outputs  
  
```  
AsyncMessageStream executor stopped: 0  
Handler executor stopped: 0  
EXECUTOR: 0 1  
Success  
```  
  
I guess there is a need for a second work guard here -> https://github.com/boostorg/beast/blob/develop/include/boost/beast/_experimental/test/impl/stream.hpp#L459 ?
