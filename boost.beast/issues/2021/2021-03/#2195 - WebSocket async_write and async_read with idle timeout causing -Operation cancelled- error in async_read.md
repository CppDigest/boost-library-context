# #2195 - WebSocket async_write and async_read with idle timeout causing "Operation cancelled" error in async_read [Closed]

> Username: AnthonySabbagh  
> Created at: 2021-03-16 15:34:42 UTC  
> Updated at: 2021-03-16 21:22:01 UTC  
> Closed at: 2021-03-16 21:22:01 UTC  
> Url: https://github.com/boostorg/beast/issues/2195  

### Version of Beast  
  
306  
  
### Steps necessary to reproduce the problem  
I have built a simple websocket client based on the asynchronous websocket client example. I have censored the message contents and the host and port of the server, but everything else I have pasted below.  
  
Everything works as expected except if I have an idle timeout set, the async_read callback is called with the error "Operation cancelled". If the idle timeout is set to 5 seconds, the error occurs just under 5 seconds into operation. If the timeout is set to 30 seconds, the error occurs closer to 30 seconds into operation. What I suspect is happening is that the keep alive ping and the write are interfering with each other. I suspect this also because if I disable reading or disable writing, no errors occur.   
  
From what I could understand from documentation async_read and async_write can be called at the same time. What am I doing wrong here?  
  
If it's important, the server this client is connecting to is written in javascript using this library https://github.com/websockets/ws.  
  
Running the below code using the host and port of a websocket server using the above library causes the issue to occur within 5 seconds. After the "Operation cancelled" error is displayed, no messages are successfully written or read.  
```  
#include <boost/beast/core.hpp>  
#include <boost/beast/websocket.hpp>  
#include <boost/asio/strand.hpp>  
#include <cstdlib>  
#include <functional>  
#include <iostream>  
#include <memory>  
#include <string>  
#include <chrono>  
#include <queue>  
#include <thread>  
  
namespace beast = boost::beast;         // from <boost/beast.hpp>  
namespace http = beast::http;           // from <boost/beast/http.hpp>  
namespace websocket = beast::websocket; // from <boost/beast/websocket.hpp>  
namespace net = boost::asio;            // from <boost/asio.hpp>  
using tcp = boost::asio::ip::tcp;       // from <boost/asio/ip/tcp.hpp>  
  
//------------------------------------------------------------------------------  
  
// Report a failure  
void  
fail(beast::error_code ec, char const* what)  
{  
    std::cerr << what << ": " << ec.message() << "\n";  
}  
  
void  
default_on_message(std::string message)  
{  
    std::cout << "message received: " << message << std::endl;  
}  
  
// Sxnds a WebSocket message and prints the response  
class session : public std::enable_shared_from_this<session>  
{  
    tcp::resolver resolver_;  
    websocket::stream<beast::tcp_stream> ws_;  
    beast::flat_buffer buffer_;  
    std::string host_;  
    std::string text_;  
    std::function<void(std::string)> on_message_;  
    std::queue<std::string> message_queue_; //messages to send  
  
public:  
    // Resolver and socket require an io_context  
    explicit  
    session(net::io_context& ioc, std::function<void(std::string)> on_message = default_on_message)  
        : resolver_(net::make_strand(ioc))  
        , ws_(net::make_strand(ioc))  
    {  
       on_message_ = on_message;   
    }  
  
    // Start the asynchronous operation  
    void  
    run(  
        char const* host,  
        char const* port,  
        char const* text)  
    {  
        // Save these for later  
        host_ = host;  
        text_ = text;  
  
        // Look up the domain name  
        resolver_.async_resolve(  
            host,  
            port,  
            beast::bind_front_handler(  
                &session::on_resolve,  
                shared_from_this()));  
    }  
  
    void  
    on_resolve(  
        beast::error_code ec,  
        tcp::resolver::results_type results)  
    {  
        if(ec)  
            return fail(ec, "resolve");  
  
        // Set the timeout for the operation  
        beast::get_lowest_layer(ws_).expires_after(std::chrono::seconds(30));  
  
        // Make the connection on the IP address we get from a lookup  
        beast::get_lowest_layer(ws_).async_connect(  
            results,  
            beast::bind_front_handler(  
                &session::on_connect,  
                shared_from_this()));  
    }  
  
    void  
    on_connect(beast::error_code ec, tcp::resolver::results_type::endpoint_type ep)  
    {  
        if(ec)  
            return fail(ec, "connect");  
  
        // Turn off the timeout on the tcp_stream, because  
        // the websocket stream has its own timeout system.  
        beast::get_lowest_layer(ws_).expires_never();  
  
        // Set suggested timeout settings for the websocket  
        websocket::stream_base::timeout opt{  
            std::chrono::seconds(5),  
#if 1  
            std::chrono::seconds(5),  
            true,  
#endif  
#if 0  
            websocket::stream_base::none(),  
            false,  
#endif  
        };  
        ws_.set_option(opt);  
        // Set a decorator to change the User-Agent of the handshake  
        ws_.set_option(websocket::stream_base::decorator(  
            [](websocket::request_type& req)  
            {  
                req.set(http::field::user_agent,  
                    std::string(BOOST_BEAST_VERSION_STRING) +  
                        " websocket-client-async");  
            }));  
  
        // Update the host_ string. This will provide the value of the  
        // Host HTTP header during the WebSocket handshake.  
        // See https://tools.ietf.org/html/rfc7230#section-5.4  
        host_ += ':' + std::to_string(ep.port());  
  
        // Perform the websocket handshake  
        ws_.async_handshake(host_, "/",  
            beast::bind_front_handler(  
                &session::on_handshake,  
                shared_from_this()));  
    }  
  
    void  
    on_handshake(beast::error_code ec)  
    {  
        if(ec)  
            return fail(ec, "handshake");  
          
        //start reading from Operations Dashboard  
        ws_.async_write(  
            net::buffer(text_),  
            beast::bind_front_handler(  
                &session::on_first_write,  
                shared_from_this()));  
    }  
  
    void  
    on_first_write(  
        beast::error_code ec,  
        std::size_t bytes_transferred)  
    {  
        boost::ignore_unused(bytes_transferred);  
  
        if(ec)  
            return fail(ec, "write");  
           
        write_message();  
        ws_.async_read(  
            buffer_,  
            beast::bind_front_handler(  
                &session::on_read,  
                shared_from_this()));  
    }  
  
    void  
    on_read(  
        beast::error_code ec,  
        std::size_t bytes_transferred)  
    {  
        boost::ignore_unused(bytes_transferred);  
  
  
        if(ec)  
            return fail(ec, "read");  
       // std::cout<<"reading"<<std::endl;  
       // std::cout << beast::make_printable(buffer_.data()) << std::endl;  
        std::string message = beast::buffers_to_string(buffer_.cdata());  
        on_message_(message);  
        buffer_.clear();  
        ws_.async_read(  
            buffer_,  
            beast::bind_front_handler(  
                &session::on_read,  
                shared_from_this()));  
    }  
  
    void  
    on_close(beast::error_code ec)  
    {  
        if(ec)  
            return fail(ec, "close");  
  
        // If we get here then the connection is closed gracefully  
  
        // The make_printable() function helps print a ConstBufferSequence  
        std::cout << beast::make_printable(buffer_.data()) << std::endl;  
    }  
  
    void write_message()  
    {  
        if (not message_queue_.empty()){  
            ws_.async_write( //can't call this function again before it returns  
                net::buffer((std::string)message_queue_.front()),  
                beast::bind_front_handler(  
                    &session::on_write_message,  
                    shared_from_this()));  
        }  
    }  
  
    void on_write_message(  
        beast::error_code ec,  
        std::size_t bytes_transferred)  
    {  
        message_queue_.pop();  
  
        while (message_queue_.empty());  
        ws_.async_write( //can't call this function again before it returns  
            net::buffer((std::string)message_queue_.front()),  
            beast::bind_front_handler(  
                &session::on_write_message,  
                shared_from_this()));  
    }  
  
    void send_message(std::string message){  
        message_queue_.push(message);  
    }  
};  
  
//------------------------------------------------------------------------------  
void sending_messages(std::shared_ptr<session> s){  
    while (true) {  
        std::string message = "message content";  
        s->send_message(message);  
        std::cout<<"sending"<<std::endl;  
        std::this_thread::sleep_for(std::chrono::milliseconds(500));  
    }  
}  
  
int main(int argc, char** argv)  
{  
    auto const host = "111.111.111.111";  
    auto const port = "1111";  
    auto const text =  "first message";  
  
    // The io_context is required for all I/O  
    net::io_context ioc;  
  
    // Launch the asynchronous operation  
    auto s = std::make_shared<session>(ioc);  
    s->run(host, port, text);  
  
    std::thread input_thread (sending_messages, s->shared_from_this());  
  
    // Run the I/O service. The call will return when  
    // the socket is closed.  
    ioc.run();  
  
    return EXIT_SUCCESS;  
}  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2021-03-16 15:52:54 UTC  
> Url: https://github.com/boostorg/beast/issues/2195#issuecomment-800383203  

Looks like an infinite loop here:  
```  
while (message_queue_.empty());  
```  
You are blocking the I/O context thread, so no more callbacks are delivered.

---

## Comment 2

> Username: AnthonySabbagh  
> Created at: 2021-03-16 16:17:24 UTC  
> Url: https://github.com/boostorg/beast/issues/2195#issuecomment-800403277  

Thanks for the help. I will restructure the code so that it doesn't do that and will update if it is fixed.

---

## Comment 3

> Username: AnthonySabbagh  
> Created at: 2021-03-16 21:22:01 UTC  
> Url: https://github.com/boostorg/beast/issues/2195#issuecomment-800614728  

Seem to work fine now if I don't block the thread.
