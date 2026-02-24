# #2951 - which situation could cause the soft_mutex unlock error [Closed]

> Username: pa1gebro  
> Created at: 2024-11-13 18:25:41 UTC  
> Updated at: 2024-11-15 15:54:51 UTC  
> Closed at: 2024-11-15 15:54:49 UTC  
> Url: https://github.com/boostorg/beast/issues/2951  

I met the error, when use the websocket async_write and got the error 'assert failed id_ == T::id', but I dont know which situation could cause this ?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2024-11-13 19:10:31 UTC  
> Url: https://github.com/boostorg/beast/issues/2951#issuecomment-2474502757  

is there a comment in the source code above the line containing the assert?

---

## Comment 2

> Username: pa1gebro  
> Created at: 2024-11-13 19:55:41 UTC  
> Url: https://github.com/boostorg/beast/issues/2951#issuecomment-2474639994  

> is there a comment in the source code above the line containing the assert?  
  
nope, there is only a comments on the method `try_lock(T const*)` but none on `unlock`. But the problem, which I met, is I use the strand pattern to serialize the async_write initiation recurve forever, suddenly met the SIGABORT that told me the `id_ == T::id` assertion on the soft_mutex unlock method failed.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2024-11-13 21:28:48 UTC  
> Url: https://github.com/boostorg/beast/issues/2951#issuecomment-2474823561  

So you don't see something relevant here?  
https://github.com/boostorg/beast/blob/a21250d12d0eb2cff9d2e4ec87bf869938dea64c/include/boost/beast/websocket/detail/soft_mutex.hpp#L83

---

## Comment 4

> Username: pa1gebro  
> Created at: 2024-11-13 22:09:59 UTC  
> Url: https://github.com/boostorg/beast/issues/2951#issuecomment-2474926261  

> So you don't see something relevant here?  
>   
> [beast/include/boost/beast/websocket/detail/soft_mutex.hpp](https://github.com/boostorg/beast/blob/a21250d12d0eb2cff9d2e4ec87bf869938dea64c/include/boost/beast/websocket/detail/soft_mutex.hpp#L83)  
>   
> Line 83 in [a21250d](/boostorg/beast/commit/a21250d12d0eb2cff9d2e4ec87bf869938dea64c)  
>   
>  // If this assert goes off it means you are attempting to  
  
I have seen it. But It is interesting that the whole system is only one thread, and initiate next write operations in previous operation completion handler, but about some messages sent, suddenly, core dump raised

---

## Comment 5

> Username: vinniefalco  
> Created at: 2024-11-13 22:24:20 UTC  
> Url: https://github.com/boostorg/beast/issues/2951#issuecomment-2474946551  

You can get that assert even with one thread. For example, this will cause the assert:  
```  
void f( auto& stream, asio::const_buffer buf )  
{  
   stream.async_write( buf );  
   stream.async_write( buf );  
}  
```  
  
Two writes at the same time without an intermediate completion.

---

## Comment 6

> Username: pa1gebro  
> Created at: 2024-11-13 22:57:57 UTC  
> Url: https://github.com/boostorg/beast/issues/2951#issuecomment-2474993520  

> You can get that assert even with one thread. For example, this will cause the assert:  
>   
> ```  
> void f( auto& stream, asio::const_buffer buf )  
> {  
>    stream.async_write( buf );  
>    stream.async_write( buf );  
> }  
> ```  
>   
> Two writes at the same time without an intermediate completion.  
  
Hmmm, here is the demo code equal to my code   
  
```c++  
  
std::queue<std::string> message_queue;  
boost::asio::io_context ioc;  
boost::beast::websocket::stream<asio::local_stream::socket> socket;  
bool writing = false  
  
void do_write() {  
    if(writing) return;  
    writing = true;  
     auto message = messagq_queue.front();  
     socket.async_write(message. [](ec, byte_transfered){  
         if(!ec) {  
          message_queue.pop();  
          if (!message_queue.empty()) do_write();  
          else writing=false;  
        }  
    });  
}  
  
void producer() {  
    ioc.post([&](){  
        message_queue.push("message");  
        if(!writing) do_write();  
   })  
}  
  
  
  
ioc.run();  
  
```  
  
After a period, the system will core dump with the write_op::operator() inner the soft_mutex::unlock `id_ == T::id` assertion failed.

---

## Comment 7

> Username: vinniefalco  
> Created at: 2024-11-14 02:31:15 UTC  
> Updated at: 2024-11-14 02:31:33 UTC  
> Url: https://github.com/boostorg/beast/issues/2951#issuecomment-2475278779  

That code does look good. Can you make a small program which reproduces the defect?

---

## Comment 8

> Username: pa1gebro  
> Created at: 2024-11-14 06:38:31 UTC  
> Url: https://github.com/boostorg/beast/issues/2951#issuecomment-2475527530  

> That code does look good. Can you make a small program which reproduces the defect?  
  
same as the demo I have wrote but the producer function is running in multi-thread environment , there are many concurrent producers working until receive a signal

---

## Comment 9

> Username: ashtum  
> Created at: 2024-11-14 07:02:27 UTC  
> Url: https://github.com/boostorg/beast/issues/2951#issuecomment-2475564315  

To access `websocket::stream` from multiple threads, you need to consider the following:  
  
1. If you're using a multi-threaded executor, wrap the executor in an ASIO [strand](https://www.boost.org/doc/libs/1_86_0/doc/html/boost_asio/reference/strand.html) to avoid race conditions when scheduling completion handlers. You can see an example here: (https://github.com/boostorg/beast/blob/develop/example/websocket/client/async/websocket_client_async.cpp#L52.  
  
2. When accessing the `websocket::stream` object (for calling any member function not just asynchronous operations), post your work to that strand:  
   ```cpp  
   asio::post(ws.get_executor(), [&](){  
       // Safe to call ws object interfaces here  
       ws.async_close(...);  
   });  
   ```

---

## Comment 10

> Username: pa1gebro  
> Created at: 2024-11-14 08:19:34 UTC  
> Url: https://github.com/boostorg/beast/issues/2951#issuecomment-2475693331  

> To access `websocket::stream` from multiple threads, you need to consider the following:  
>   
> 1. If you're using a multi-threaded executor, wrap the executor in an ASIO [strand](https://www.boost.org/doc/libs/1_86_0/doc/html/boost_asio/reference/strand.html) to avoid race conditions when scheduling completion handlers. You can see an example here: (https://github.com/boostorg/beast/blob/develop/example/websocket/client/async/websocket_client_async.cpp#L52.  
> 2. When accessing the `websocket::stream` object (for calling any member function not just asynchronous operations), post your work to that strand:  
>    asio::post(ws.get_executor(), [&](){  
>        // Safe to call ws object interfaces here  
>        ws.async_close(...);  
>    });  
  
I have only one io_context and only on thread work for IO not only accept but session read write, but the business threads will always call post to post message and distribute these messages into every session, just a simple pub/sub model. And the bug is always raise on the sofet_mutex unlock method. I dont think, this bug is derived from the overlaping async_write calls, because if the overlaping async_write always raised error on try_lock not unlock

---

## Comment 11

> Username: ashtum  
> Created at: 2024-11-14 08:24:23 UTC  
> Url: https://github.com/boostorg/beast/issues/2951#issuecomment-2475701907  

> I have only one io_context and only on thread work for IO not only accept but session read write, but the business threads will always call post to post message and distribute these messages into every session, just a simple pub/sub model. And the bug is always raise on the sofet_mutex unlock method. I dont think, this bug is derived from the overlaping async_write calls, because if the overlaping async_write always raised error on try_lock not unlock  
  
At this point, it's hard to guess what the issue might be. Could you provide a minimal reproducible example?

---

## Comment 12

> Username: pa1gebro  
> Created at: 2024-11-14 08:37:14 UTC  
> Url: https://github.com/boostorg/beast/issues/2951#issuecomment-2475725331  

> > I have only one io_context and only on thread work for IO not only accept but session read write, but the business threads will always call post to post message and distribute these messages into every session, just a simple pub/sub model. And the bug is always raise on the sofet_mutex unlock method. I dont think, this bug is derived from the overlaping async_write calls, because if the overlaping async_write always raised error on try_lock not unlock  
>   
> At this point, it's hard to guess what the issue might be. Could you provide a minimal reproducible example?  
  
I am working for this demo, move all business into all in one mode

---

## Comment 13

> Username: pa1gebro  
> Created at: 2024-11-15 07:47:20 UTC  
> Updated at: 2024-11-15 07:47:46 UTC  
> Url: https://github.com/boostorg/beast/issues/2951#issuecomment-2478155677  

Here is my example code to show this case , all async_write in one strand still core dump   
  
```c++  
#include <boost/asio.hpp>  
#include <boost/beast.hpp>  
#include <boost/thread.hpp>  
#include <iostream>  
#include <memory>  
#include <queue>  
#include <set>  
#include <thread>  
#include <vector>  
  
namespace beast = boost::beast;  
namespace net = boost::asio;  
namespace ws = beast::websocket;  
  
  
class WebSocketSession : public std::enable_shared_from_this<WebSocketSession> {  
   public:  
    WebSocketSession(boost::asio::local::stream_protocol::socket socket) : ws_(std::move(socket)) {}  
  
    void start() { doAccept(); }  
  
    void send(std::shared_ptr<std::string const>& message) {  
        json_message_queue_.push(message);  
        if (json_message_queue_.size() == 1) doCheckWrite();  
    }  
  
   private:  
    ws::stream<boost::asio::local::stream_protocol::socket> ws_;  
    beast::flat_buffer buffer_;  
  
    void doAccept() {  
        std::cout << "session doAccept" << std::endl;  
        ws_.async_accept([self = shared_from_this()](boost::system::error_code ec) {  
            if (!ec) {  
                self->doRead();  
            }  
        });  
    }  
  
    void doRead() {  
        ws_.async_read(buffer_,  
                       [self = shared_from_this()](boost::system::error_code ec,  
                                                   std::size_t bytes_transferred) {  
                           if (!ec) {  
                               std::cout << "Received message: "  
                                         << beast::buffers_to_string(self->buffer_.data())  
                                         << std::endl;  
                               self->buffer_.consume(bytes_transferred);  
                               self->doRead();  
                           }  
                       });  
    }  
  
   private:  
    std::queue<std::shared_ptr<std::string const>> json_message_queue_;  
  
    void doCheckWrite() {  
        if (json_message_queue_.size() > 0) {  
            auto message = json_message_queue_.front();  
            ws_.text(true);  
            ws_.async_write(boost::asio::buffer(message->data(), message->size()),  
                            [this, self = shared_from_this()](const std::error_code& ec,  
                                                              std::size_t bytes_transferred) {  
                                if (ec) {  
                                    std::cout << "async_write error: " << ec.message() << std::endl;  
                                } else {  
                                    json_message_queue_.pop();  
                                    this->doCheckWrite();  
                                }  
                            });  
        }  
    }  
};  
  
class SessionScheduler {  
   public:  
    SessionScheduler(boost::asio::io_context& ioc,  
                     boost::asio::strand<boost::asio::io_context::executor_type>& strand)  
        : ioc_(ioc), strand_(strand) {}  
  
    void registerSession(std::shared_ptr<WebSocketSession> session) {  
        sessions_set_.insert(session);  
    }  
  
    void unRegisterSession(std::shared_ptr<WebSocketSession> session) {  
        // TODO: Remove  
    }  
  
    void sendMessage(std::shared_ptr<std::string const> message) {  
        boost::asio::post(strand_, std::bind(&SessionScheduler::doSendMessage, this, message));  
    }  
  
    void doSendMessage(std::shared_ptr<std::string const> message) {  
        for (auto& session : sessions_set_) {  
            session->send(message);  
        }  
    }  
  
   private:  
    std::set<std::shared_ptr<WebSocketSession>> sessions_set_;  
    boost::asio::io_context& ioc_;  
    boost::asio::strand<boost::asio::io_context::executor_type>& strand_;  
};  
  
class UnixSocketServer {  
   public:  
    UnixSocketServer(net::io_context& ioc,  
                     const std::string& socket_path,  
                     SessionScheduler* scheduler,  
                     boost::asio::strand<boost::asio::io_context::executor_type>& strand)  
        : ioc_(ioc), acceptor_(ioc), scheduler_(scheduler), strand_(strand) {  
        boost::asio::local::stream_protocol::endpoint endpoint(socket_path.data());  
        boost::system::error_code ec;  
        acceptor_.open(endpoint.protocol(), ec);  
        if (ec) {  
            std::cout << "Failed to open file" << socket_path << " " << ec.message() << std::endl;  
            exit(1);  
        }  
        acceptor_.bind(endpoint, ec);  
        if (ec) {  
            std::cout << "Failed to bind to endpoint" << socket_path << " " << ec.message()  
                      << std::endl;  
            exit(1);  
        }  
        acceptor_.listen();  
    }  
  
    void run() {  
        std::cout << "Starting server on " << acceptor_.local_endpoint() << std::endl;  
        doAccept();  
    }  
  
   private:  
    boost::asio::local::stream_protocol::acceptor acceptor_;  
    SessionScheduler* scheduler_;  
    boost::asio::strand<boost::asio::io_context::executor_type>& strand_;  
    boost::asio::io_context& ioc_;  
  
    void doAccept() {  
        acceptor_.async_accept(  
            strand_,  
            [this](boost::system::error_code ec,  
                   boost::asio::local::stream_protocol::socket socket) {  
                if (!ec) {  
                    std::cout << "New connection accepted" << std::endl;  
                    auto session = std::make_shared<WebSocketSession>(std::move(socket));  
                    scheduler_->registerSession(session);  
                    session->start();  
                } else {  
                    std::cout << "doAccept failed: " << ec.message() << std::endl;  
                }  
                doAccept();  
            });  
    }  
};  
  
int main() {  
    try {  
        net::io_context ioc;  
        boost::asio::strand<boost::asio::io_context::executor_type> strand = net::make_strand(ioc);  
        SessionScheduler scheduler(ioc, strand);  
        const std::string socket_path = "/tmp/websocket_server.sock";  
        UnixSocketServer server(ioc, socket_path, &scheduler, strand);  
  
        std::thread server_thread([&ioc, &server]() {  
            server.run();  
            ioc.run();  
        });  
  
        const int num_writer_threads = 5;  
        std::vector<std::thread> writer_threads;  
  
        for (int i = 0; i < num_writer_threads; ++i) {  
            writer_threads.emplace_back([&scheduler, i]() {  
                while (true) {  
                    std::this_thread::sleep_for(std::chrono::milliseconds(1));  // sleep 30ms  
                    std::shared_ptr<std::string> msg = std::make_shared<std::string>(  
                        "Hello from server! Thread " + std::to_string(i));  
                    scheduler.sendMessage(msg);  
                }  
            });  
        }  
        server_thread.join();  
        for (auto& t : writer_threads) {  
            t.join();  
        }  
  
    } catch (const std::exception& e) {  
        std::cerr << "Error: " << e.what() << std::endl;  
    }  
  
    return 0;  
}  
  
```

---

## Comment 14

> Username: ashtum  
> Created at: 2024-11-15 09:01:12 UTC  
> Url: https://github.com/boostorg/beast/issues/2951#issuecomment-2478279349  

You're calling `async_write` while an `async_handshake` is still in progress. You can either delay stream registration until the handshake is complete or check `ws_.is_open()` before scheduling a write, which returns `false` until the handshake is successful.

---

## Comment 15

> Username: pa1gebro  
> Created at: 2024-11-15 15:54:49 UTC  
> Url: https://github.com/boostorg/beast/issues/2951#issuecomment-2479292297  

thx for your reply, I have fixed this error. I will close this issue
