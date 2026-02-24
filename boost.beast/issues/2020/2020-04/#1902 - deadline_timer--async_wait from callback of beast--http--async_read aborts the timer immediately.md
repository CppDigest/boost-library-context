# #1902 - deadline_timer::async_wait from callback of beast::http::async_read aborts the timer immediately [Closed]

> Username: neel  
> Created at: 2020-04-15 12:47:32 UTC  
> Updated at: 2020-04-15 16:25:36 UTC  
> Closed at: 2020-04-15 16:25:35 UTC  
> Url: https://github.com/boostorg/beast/issues/1902  

I don't know how to pin point the problem. But it looks like calling `async_wait` from an http session is immediately aborting a deadline timer.   
  
I am setting up long polling using boost beast. I am using the [example in boost website](https://www.boost.org/doc/libs/1_70_0/libs/beast/example/http/server/async/http_server_async.cpp). Renamed session to connection. Different methods are called depending on the HTTP path.  
  
```  
struct delayed{  
    boost::asio::deadline_timer _timer;  
    delayed(boost::asio::io_service& io): _timer(io){}  
    void triggered(udho::contexts::stateless ctx, const boost::system::error_code& e){  
        std::cout << "timer error: " << e << std::endl;  
    }  
    void operator()(udho::contexts::stateless ctx){  
        _timer.expires_from_now(boost::posix_time::seconds(15));  
        _timer.async_wait(boost::bind(&delayed::triggered, this, ctx, boost::asio::placeholders::error));  
        std::cout << "timer setup" << std::endl;  
    }  
}  
```  
      
`delayed` is instantiated in main by passing the `io_service` that is used for `beast`. The `operator()` is being invoked from an HTTP request through that connection (session in the example) class, based on a certain path mapped.  
  
The problem is the timer is aborted immediately (system:125). However if the `async_wait` is called from main then it works fine. Does it have to do something with the `strand` in the connection class ?  
  
The function is being called from `connection::on_read`  which is being called through a strand.   
  
```  
// connection class  
void do_read(){  
    _req = {};  
    http::async_read(_socket, _buffer, _req, boost::asio::bind_executor(_strand, std::bind(&self_type::on_read, std::enable_shared_from_this<connection<RouterT, AttachmentT>>::shared_from_this(), std::placeholders::_1, std::placeholders::_2)));  
}  
```  
  
Is there something like, a function being called through the strand can not perform async_wait or somethign like that ?

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-04-15 12:53:19 UTC  
> Url: https://github.com/boostorg/beast/issues/1902#issuecomment-614021050  

It's diffcult to comment without seeing a complete program.   
  
Calling timer.expires_from_now() while there is an outstanding async_wait() on the timer will cause that wait to be aborted (with an asio::error::operation_aborted error).   
  
This behaviour is documented in asio.

---

## Comment 2

> Username: neel  
> Created at: 2020-04-15 13:02:46 UTC  
> Updated at: 2020-04-15 13:08:33 UTC  
> Url: https://github.com/boostorg/beast/issues/1902#issuecomment-614026195  

Yes I understand. But the codebase is too big. and its hard to isolate. But I am trying to describe the flow.  
  
On accept the `listener` creates a `connection` with the socket (moved).   
listener calls `run` method of the connection.  
Then the `connection` class starts a read loop.   
```  
http::async_read(_socket, _buffer, _req, boost::asio::bind_executor(_strand, std::bind(&self_type::on_read, std::enable_shared_from_this<connection<RouterT, AttachmentT>>::shared_from_this(), std::placeholders::_1, std::placeholders::_2)));  
```  
The `on_read` method uses the request and calls an appropriate method  
  
Is it possible to call `async_wait` from such a method ? Whats the alternative way then ?  
  
@madmongo1 It looks like I am calling `deadline_timer::async_wait` from a function which is a callback of  `boost::beast::http::async_read`. Is this the scenario which you are talking about ?  
  
There is no outstanding `async_wait` on that timer.

---

## Comment 3

> Username: jpramosi  
> Created at: 2020-04-15 15:37:21 UTC  
> Updated at: 2020-04-15 15:43:24 UTC  
> Url: https://github.com/boostorg/beast/issues/1902#issuecomment-614113849  

I think it would help if you describe what exactly your goal with the timer is. Maybe provide some kind of raw pseudo-code with the work-flow which shows what do you would like to achieve.  
Roughly speaking timers are often defined in the session. If you would like to start a timed task, you could initiate it like here:  
  
```cpp  
  
    boost::asio::steady_timer timer_;  
  
    session() :  
        // ...  
        timer_(stream_.get_executor(),  
               (std::chrono::steady_clock::time_point::max)())  
        // ...  
    {  
    }  
  
    void post_start_my_task()  
    {  
        boost::asio::post(  
            stream_.get_executor(),  
            boost::beast::bind_front_handler(&session::start_my_task,  
                                             this->shared_from_this()));  
    }  
  
    void start_my_task()  
    {  
        // start_my_task() must be in the session its executor!, if you are not  
        // on session its execution context, use post_start_my_task() instead  
        timer_.expires_after(std::chrono::seconds(15));  
  
        // after 15 secs fire on_start_my_task  
        timer_.async_wait(boost::beast::bind_front_handler(  
            &session::on_start_my_task, shared_from_this()));  
  
        // If an async_wait() operation is active and not finished and  
        // you are going to close the session manually, dont forget to cancel the  
        // timer  
    }  
  
    void on_start_my_task(boost::beast::error_code ec)  
    {  
        // Timer was canceled  
        if (ec == boost::asio::error::operation_aborted)  
            return;  
  
        // Check whether the timer is actually expired  
        if (timer_->expiry() <= std::chrono::steady_clock::now())  
            return;  
  
        // Now any other async operation can be initiated here to keep the session  
        // open  
    }  
```  
  
A more detailed example with timers can be seen [here](https://www.boost.org/doc/libs/1_69_0/libs/beast/example/advanced/server/advanced_server.cpp).  
Even if this example is for a server, the techniques are applicable for clients as well.  
However it is an old documentation and may contain bugs. I recommend to be very careful and compare it with the recent version of Boost.Beast.  
  
I know it may not resolve your problem but I hope i could help a little bit to find maybe another way.

---

## Comment 4

> Username: neel  
> Created at: 2020-04-15 16:19:56 UTC  
> Url: https://github.com/boostorg/beast/issues/1902#issuecomment-614137830  

I found the problem. It was chrome sending multiple http requests, which was calling `expires_from_now` and `async_wait` multiple times. But the real problem is to perform long polling. The server will not return a response until a timeout of 15 seconds. I have attached a minimum reproducible example. Although the timer works fine the objective is not accomplished. I want `delayed::operator()` to start the timer only. But the socket should be kept open, and the client should be waiting for 15 seconds. Here the client gets an empty response. Which is not what I wanted. I expected as the `triggered` method is not calling the send lambda, the client should be waiting forever. But this is not happening. I understand that the `session` object has to be kept alive in order to do that. I my real application I am keeping the session object in a vector. But even if the `session` object dies the client should not get an empty response. The client should be waiting for a response.  
  
```  
#include <boost/beast/core.hpp>  
#include <boost/beast/http.hpp>  
#include <boost/beast/version.hpp>  
#include <boost/asio/strand.hpp>  
#include <boost/config.hpp>  
#include <boost/asio/deadline_timer.hpp>  
#include <boost/asio/placeholders.hpp>  
#include <boost/bind.hpp>  
#include <algorithm>  
#include <cstdlib>  
#include <functional>  
#include <iostream>  
#include <memory>  
#include <string>  
#include <thread>  
#include <vector>  
  
namespace beast = boost::beast;         // from <boost/beast.hpp>  
namespace http = beast::http;           // from <boost/beast/http.hpp>  
namespace net = boost::asio;            // from <boost/asio.hpp>  
using tcp = boost::asio::ip::tcp;       // from <boost/asio/ip/tcp.hpp>  
  
struct delayed{  
    boost::asio::deadline_timer _timer;  
    bool _running;  
      
    delayed(boost::asio::io_context& io): _timer(io), _running(false){}  
    /**  
     * The triggered method will call the send lambda with a response  
     * The corresponding session will be stored in a vector until the   
     * triggered method is fired. The session will be removed from the  
     * vector after triggered() is invoked.  
     */  
    void triggered(const boost::system::error_code& e){  
        std::cout << "timer error: " << e << std::endl;  
        _running = false;  
    }  
    void operator()(){  
        if(!_running){  
            _timer.expires_from_now(boost::posix_time::seconds(15));  
            _timer.async_wait(boost::bind(&delayed::triggered, this, boost::asio::placeholders::error));  
            std::cout << "timer setup" << std::endl;  
            _running = true;  
        }  
    }  
};  
  
// Report a failure  
void fail(beast::error_code ec, char const* what){  
    std::cerr << what << ": " << ec.message() << "\n";  
}  
  
// Handles an HTTP server connection  
template <typename F>  
class session : public std::enable_shared_from_this<session<F>>{  
    typedef session<F> self_type;  
      
    struct send_lambda{  
        session& self_;  
  
        explicit send_lambda(session& self): self_(self){}  
  
        template<bool isRequest, class Body, class Fields>  
        void operator()(http::message<isRequest, Body, Fields>&& msg) const{  
            auto sp = std::make_shared<http::message<isRequest, Body, Fields>>(std::move(msg));  
            self_.res_ = sp;  
            http::async_write(self_.stream_,*sp,beast::bind_front_handler(&session::on_write,self_.shared_from_this(),sp->need_eof()));  
        }  
    };  
  
    typedef F callback_type;  
      
    callback_type&   _callback;  
    beast::tcp_stream stream_;  
    beast::flat_buffer buffer_;  
    std::shared_ptr<std::string const> doc_root_;  
    http::request<http::string_body> req_;  
    std::shared_ptr<void> res_;  
    send_lambda lambda_;  
  
public:  
    // Take ownership of the stream  
    session(callback_type& callback, tcp::socket&& socket, std::shared_ptr<std::string const> const& doc_root): _callback(callback), stream_(std::move(socket)), doc_root_(doc_root), lambda_(*this){}  
  
    // Start the asynchronous operation  
    void run(){  
        do_read();  
    }  
  
    void do_read(){  
        req_ = {};  
        stream_.expires_after(std::chrono::seconds(30));  
        http::async_read(stream_, buffer_, req_, beast::bind_front_handler(&session::on_read, self_type::shared_from_this()));  
    }  
  
    void on_read(beast::error_code ec,std::size_t bytes_transferred){  
        boost::ignore_unused(bytes_transferred);  
        if(ec == http::error::end_of_stream)  
            return do_close();  
        if(ec)  
            return fail(ec, "read");  
  
        // Send the response  
//         handle_request(*doc_root_, std::move(req_), lambda_);  
        _callback();  
    }  
  
    void on_write(bool close,beast::error_code ec,std::size_t bytes_transferred){  
        boost::ignore_unused(bytes_transferred);  
        if(ec) return fail(ec, "write");  
        if(close) return do_close();  
        res_ = nullptr;  
        do_read();  
    }  
  
    void do_close(){  
        beast::error_code ec;  
        stream_.socket().shutdown(tcp::socket::shutdown_send, ec);  
    }  
};  
  
//------------------------------------------------------------------------------  
  
// Accepts incoming connections and launches the sessions  
template <typename F>  
class listener : public std::enable_shared_from_this<listener<F>>{  
    typedef F callback_type;  
    typedef listener<F> self_type;  
      
    callback_type&   _callback;  
    net::io_context& ioc_;  
    tcp::acceptor acceptor_;  
    std::shared_ptr<std::string const> doc_root_;  
  
public:  
    listener(callback_type& callback, net::io_context& ioc, tcp::endpoint endpoint, std::shared_ptr<std::string const> const& doc_root) : _callback(callback), ioc_(ioc), acceptor_(net::make_strand(ioc)), doc_root_(doc_root){  
        beast::error_code ec;  
        acceptor_.open(endpoint.protocol(), ec);  
        if(ec){  
            fail(ec, "open");  
            return;  
        }  
        acceptor_.set_option(net::socket_base::reuse_address(true), ec);  
        if(ec){  
            fail(ec, "set_option");  
            return;  
        }  
        acceptor_.bind(endpoint, ec);  
        if(ec){  
            fail(ec, "bind");  
            return;  
        }  
        acceptor_.listen(net::socket_base::max_listen_connections, ec);  
        if(ec){  
            fail(ec, "listen");  
            return;  
        }  
    }  
    void run(){  
        do_accept();  
    }  
  
private:  
    void do_accept(){  
        acceptor_.async_accept(net::make_strand(ioc_), beast::bind_front_handler(&listener::on_accept, self_type::shared_from_this()));  
    }  
  
    void on_accept(beast::error_code ec, tcp::socket socket){  
        if(ec){  
            fail(ec, "accept");  
        }else{  
            std::make_shared<session<F>>(_callback, std::move(socket), doc_root_)->run();  
        }  
        do_accept();  
    }  
};  
  
//------------------------------------------------------------------------------  
  
int main(int argc, char* argv[]){  
    // Check command line arguments.  
    if (argc != 5){  
        std::cerr <<  
            "Usage: http-server-async <address> <port> <doc_root> <threads>\n" <<  
            "Example:\n" <<  
            "    http-server-async 0.0.0.0 8080 . 1\n";  
        return EXIT_FAILURE;  
    }  
    auto const address = net::ip::make_address(argv[1]);  
    auto const port = static_cast<unsigned short>(std::atoi(argv[2]));  
    auto const doc_root = std::make_shared<std::string>(argv[3]);  
    auto const threads = std::max<int>(1, std::atoi(argv[4]));  
  
    // The io_context is required for all I/O  
    net::io_context ioc{threads};  
  
    delayed d1(ioc);  
//     d1();  
    // Create and launch a listening port  
    std::make_shared<listener<delayed>>(d1, ioc,tcp::endpoint{address, port},doc_root)->run();  
  
    // Run the I/O service on the requested number of threads  
    std::vector<std::thread> v;  
    v.reserve(threads - 1);  
    for(auto i = threads - 1; i > 0; --i){  
        v.emplace_back([&ioc]{  
            ioc.run();  
        });  
    }  
    ioc.run();  
  
    return EXIT_SUCCESS;  
}  
  
```

---

## Comment 5

> Username: neel  
> Created at: 2020-04-15 16:25:35 UTC  
> Url: https://github.com/boostorg/beast/issues/1902#issuecomment-614141005  

I am closing the issue. The actual issue is resolved. I think I should organize and open a new issue for long polling if required.
