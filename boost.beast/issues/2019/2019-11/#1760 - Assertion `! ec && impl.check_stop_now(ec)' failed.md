# #1760 - Assertion `! ec && impl.check_stop_now(ec)' failed [Closed]

> Username: kristofg  
> Created at: 2019-11-08 11:01:54 UTC  
> Updated at: 2019-11-14 12:01:11 UTC  
> Closed at: 2019-11-14 12:01:11 UTC  
> Url: https://github.com/boostorg/beast/issues/1760  

We have a bunch of websocket clients using beast, and we are seeing occasional crashes with `boost/boost/beast/websocket/impl/read.hpp:112: Assertion '! ec && impl.check_stop_now(ec)' failed.`  
  
This typically happens during bad network conditions, with connections timing out and being reopened over and over.   I have written a small-ish test program in an attempt to understand what is going on, and the program below reproduces the same assert we're seeing on our clients.  I've tested it with Boost 1.71.0 and GCC 9.2.1.  
  
The Server object accepts a connection, reads and echoes back a single message, then stops replying.  The Client connects, sets up an `async_read`, then does an `async_write` and `async_close` in close succession.  Since you can't have more than one write operation at the same time, it uses a send queue to make sure the close isn't sent until the write is finished.  Since the server isn't replying to the close message, the close times out, but it looks to me as if the previously started `async_read` gets mixed up with the `async_close` somehow.  
  
Any help in understanding what is going on here (or what I am doing wrong) would be greatly appreciated!  
  
```  
/*  
  g++ -std=c++17 -Wall -pedantic -I ~/src/boost/ -lpthread -o close-timeout close-timeout.cpp  
*/  
  
#include <boost/asio/ip/address.hpp>  
#include <boost/asio/steady_timer.hpp>  
#include <boost/asio/ts/internet.hpp>  
#include <boost/asio/ts/io_context.hpp>  
#include <boost/asio/ts/socket.hpp>  
#include <boost/beast/core.hpp>  
#include <boost/beast/websocket.hpp>  
  
#include <cassert>  
#include <deque>  
#include <iostream>  
  
using namespace std::chrono_literals;  
  
namespace {  
  
const std::string hostname = "::1";  
  
class Server {  
private:  
    boost::asio::io_context & io_context_;  
    boost::asio::ip::address address_;  
    boost::asio::ip::tcp::acceptor acceptor_;  
    std::unique_ptr<boost::asio::ip::tcp::socket> socket_;  
    std::unique_ptr<boost::beast::websocket::stream<boost::asio::ip::tcp::socket &>> stream_;  
    boost::beast::multi_buffer buffer_;  
  
public:  
    Server(boost::asio::io_context & io_context)  
        : io_context_(io_context)  
        , address_(boost::asio::ip::make_address(hostname))  
        , acceptor_(io_context_, {address_, 0})  
        , socket_(std::make_unique<boost::asio::ip::tcp::socket>(io_context_))  
        , stream_(std::make_unique<boost::beast::websocket::stream<boost::asio::ip::tcp::socket &>>(  
              *socket_))  
    {  
        stream_->set_option(boost::beast::websocket::stream_base::timeout::suggested(  
            boost::beast::role_type::server));  
    }  
    unsigned short accept()  
    {  
        acceptor_.async_accept(  
            *socket_, [this](const boost::system::error_code & ec) { socket_accept_handler(ec); });  
        return acceptor_.local_endpoint().port();  
    }  
    void socket_accept_handler(const boost::system::error_code & ec)  
    {  
        assert(!ec);  
        stream_->async_accept([this](const boost::system::error_code & ec) { on_accepted(ec); });  
    }  
    void on_accepted(const boost::system::error_code & ec)  
    {  
        assert(!ec);  
        buffer_.clear();  
        stream_->async_read(  
            buffer_, [this](const boost::system::error_code & ec, std::size_t bytes_written) {  
                on_message_read(ec, bytes_written);  
            });  
    }  
    void on_message_read(const boost::system::error_code & ec, std::size_t bytes_written)  
    {  
        assert(!ec);  
        std::cerr << __func__ << " Server read " << bytes_written << " bytes" << std::endl;  
        // Echo the first message back, then just stop:  
        stream_->async_write(  
            buffer_.data(),  
            [this](const boost::system::error_code & ec, std::size_t bytes_written) {  
                assert(!ec);  
                std::cerr << __func__ << " Server wrote " << bytes_written << " bytes" << std::endl;  
                buffer_.consume(buffer_.size());  
            });  
    }  
    bool connected() { return stream_ && stream_->is_open(); }  
};  
  
enum class event_type { send, close };  
  
class event {  
public:  
    event(event_type t, const std::string & p)  
        : type(t)  
        , payload(p)  
    {  
    }  
    event_type type;  
    std::string payload;  
  
    std::string dump()  
    {  
        switch (type) {  
        case event_type::send:  
            return "SEND '" + payload + "'";  
        case event_type::close:  
            return "CLOSE";  
        }  
        abort();  
    }  
};  
  
class Client {  
private:  
    boost::asio::io_context & io_context_;  
    std::unique_ptr<boost::beast::websocket::stream<boost::beast::tcp_stream>> stream_;  
    std::deque<event> send_queue_;  
    bool sending_;  
  
public:  
    Client(boost::asio::io_context & io_context)  
        : io_context_(io_context)  
        , stream_(std::make_unique<boost::beast::websocket::stream<boost::beast::tcp_stream>>(  
              io_context_))  
        , sending_(false)  
    {  
        stream_->set_option(boost::beast::websocket::stream_base::timeout::suggested(  
            boost::beast::role_type::client));  
    }  
    void connect(unsigned short port)  
    {  
        auto ep =  
            boost::asio::ip::tcp::endpoint(boost::asio::ip::address::from_string(hostname), port);  
        auto & tcp = boost::beast::get_lowest_layer(*stream_);  
        tcp.async_connect(ep, [this](const boost::system::error_code & ec) { on_connected(ec); });  
    }  
    void on_connected(const boost::system::error_code & ec)  
    {  
        assert(!ec);  
        stream_->async_handshake(hostname, "/", [this](const boost::system::error_code & ec) {  
            assert(!ec);  
            do_read();  
        });  
    }  
    void do_read()  
    {  
        auto buf = std::make_shared<boost::beast::multi_buffer>();  
        stream_->async_read(  
            *buf, [this, buf](const boost::system::error_code & ec, std::size_t bytes_written) {  
                if (ec) {  
                    std::cerr << "Client read error: " << ec.message() << std::endl;  
                } else {  
                    std::cerr << "Client read " << bytes_written << " bytes" << std::endl;  
                    do_read();  
                }  
            });  
    }  
    void send(std::string const & message)  
    {  
        send_queue_.emplace_back(event_type::send, message);  
        send_queued();  
    }  
    void close()  
    {  
        send_queue_.emplace_back(event_type::close, std::string());  
        send_queued();  
    }  
    void send_queued()  
    {  
        if (sending_ || send_queue_.empty())  
            return;  
  
        std::cerr << __func__ << ": " << send_queue_.front().dump() << std::endl;  
  
        sending_ = true;  
  
        switch (send_queue_.front().type) {  
        case event_type::send:  
            stream_->async_write(  
                boost::asio::buffer(send_queue_.front().payload),  
                [this](const boost::system::error_code & ec, std::size_t bytes_transferred) {  
                    assert(!ec);  
                    std::cerr << "Sent " << bytes_transferred << " bytes" << std::endl;  
                    send_queue_.pop_front();  
                    sending_ = false;  
                    send_queued();  
                });  
            break;  
        case event_type::close:  
            stream_->async_close(boost::beast::websocket::close_code::normal,  
                                 [this](const boost::system::error_code & ec) {  
                                     // The server stops, so this should time out:  
                                     assert(ec);  
                                     std::cerr << "Client close error: " << ec.message()  
                                               << std::endl;  
                                     send_queue_.pop_front();  
                                     sending_ = false;  
                                     send_queued();  
                                 });  
            break;  
        }  
    }  
    bool connected() { return stream_ && stream_->is_open(); }  
};  
  
bool run_until(boost::asio::io_context & io_context, std::function<bool()> waitfor)  
{  
    unsigned int const max_attempts = 100;  
    unsigned int tried = 0;  
    bool out_of_work = false;  
    while (!waitfor() && !out_of_work && ++tried <= max_attempts) {  
        io_context.run_one_for(60s);  
        out_of_work = io_context.stopped();  
        io_context.restart();  
    }  
    return waitfor();  
}  
  
} // namespace  
  
int main()  
{  
    boost::asio::io_context io_context;  
  
    auto s = Server(io_context);  
    auto c = Client(io_context);  
  
    auto port = s.accept();  
    c.connect(port);  
    run_until(io_context, [&] { return c.connected() && s.connected(); });  
  
    c.send("fnord");  
    c.close();  
    run_until(io_context, [] { return false; }); // run until out of work  
  
    return 0;  
}  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-11-08 16:53:15 UTC  
> Url: https://github.com/boostorg/beast/issues/1760#issuecomment-551905186  

Your program compiles and behaves correctly for me. The output:  
```  
send_queued: SEND 'fnord'  
on_message_read Server read 5 bytes  
Sent 5 bytes  
send_queued: CLOSE  
Client read 5 bytes  
operator () Server wrote 5 bytes  
Client read error: The socket was closed due to a timeout  
Client close error: The I/O operation has been aborted because of either a thread exit or an application request  
```  
  
I'm not seeing any assert. The output of the program is exactly what is expected. Do you believe this output is incorrect?

---

## Comment 2

> Username: kristofg  
> Created at: 2019-11-08 22:12:00 UTC  
> Url: https://github.com/boostorg/beast/issues/1760#issuecomment-552009664  

Sorry, I should have included the output when I run this (Linux, gcc 9.2.1, boost 1.71.0, glibc 2.29):  
```  
$ ./close-timeout   
send_queued: SEND 'fnord'  
Sent 5 bytes  
send_queued: CLOSE  
on_message_read Server read 5 bytes  
operator() Server wrote 5 bytes  
Client close error: The socket was closed due to a timeout  
close-timeout: /home/kgledits/src/boost/boost/beast/websocket/impl/read.hpp:112: void boost::beast::websocket::stream< <template-parameter-1-1>, <anonymous> >::read_some_op< <template-parameter-2-1>, <template-parameter-2-2> >::operator()(boost::beast::error_code, std::size_t, bool) [with Handler = boost::beast::websocket::stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::executor, boost::beast::unlimited_rate_policy> >::read_op<{anonymous}::Client::do_read()::<lambda(const boost::system::error_code&, std::size_t)>, boost::beast::basic_multi_buffer<std::allocator<char> > >; MutableBufferSequence = boost::beast::basic_multi_buffer<std::allocator<char> >::mutable_buffers_type; NextLayer = boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::executor, boost::beast::unlimited_rate_policy>; bool deflateSupported = true; boost::beast::error_code = boost::system::error_code; std::size_t = long unsigned int]: Assertion `! ec && impl.check_stop_now(ec)' failed.  
Aborted (core dumped)  
```  
...which *looks* like the same error we are seeing on our real clients.  
  
However, re-testing this now, I noticed I don't get the same result on current boost master (`ee14bf0cf`):  
```  
$ ./close-timeout   
send_queued: SEND 'fnord'  
Sent 5 bytes  
send_queued: CLOSE  
on_message_read Server read 5 bytes  
operator() Server wrote 5 bytes  
Client close error: The socket was closed due to a timeout  
Client read error: Operation canceled  
```  
I will take a look at the git log next, to see if I can understand what changed.

---

## Comment 3

> Username: kristofg  
> Created at: 2019-11-11 14:11:29 UTC  
> Url: https://github.com/boostorg/beast/issues/1760#issuecomment-552460662  

A quick bisect tells me commit [26a156e3](https://github.com/boostorg/beast/commit/26a156e30042299db0304a0d31ea6f0278246152) made this crash go away for me.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2019-11-11 14:36:30 UTC  
> Url: https://github.com/boostorg/beast/issues/1760#issuecomment-552470306  

Thank you very much for the heads-up!

---

## Comment 5

> Username: leleftheriades  
> Created at: 2019-11-11 15:56:37 UTC  
> Url: https://github.com/boostorg/beast/issues/1760#issuecomment-552501841  

We got the exact same assertion hit. We run it on windows MSVC. The value of ec was 0.   
We have a debugger stopped at this exception point at the moment, but we don't know how to debug it.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2019-11-11 18:34:32 UTC  
> Url: https://github.com/boostorg/beast/issues/1760#issuecomment-552560749  

Which version of Beast?

---

## Comment 7

> Username: leleftheriades  
> Created at: 2019-11-11 19:46:44 UTC  
> Url: https://github.com/boostorg/beast/issues/1760#issuecomment-552587207  

1.71

---

## Comment 8

> Username: vinniefalco  
> Created at: 2019-11-11 19:47:15 UTC  
> Url: https://github.com/boostorg/beast/issues/1760#issuecomment-552587411  

What happens when you use the tip of the **master** branch?

---

## Comment 9

> Username: kristofg  
> Created at: 2019-11-14 08:26:30 UTC  
> Url: https://github.com/boostorg/beast/issues/1760#issuecomment-553779559  

Should I just close this?  Is it a dupe of #1754?

---

## Comment 10

> Username: vinniefalco  
> Created at: 2019-11-14 11:14:11 UTC  
> Url: https://github.com/boostorg/beast/issues/1760#issuecomment-553841941  

It does look like it
