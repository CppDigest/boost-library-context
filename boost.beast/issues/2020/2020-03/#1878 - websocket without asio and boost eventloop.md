# #1878 - websocket without asio and boost eventloop [Closed]

> Username: shuras109  
> Created at: 2020-03-17 22:54:27 UTC  
> Updated at: 2020-04-09 11:07:40 UTC  
> Closed at: 2020-04-09 11:07:40 UTC  
> Url: https://github.com/boostorg/beast/issues/1878  

Hi there!  
We try to create simple class based on SyncStream to receive and sent websocket frames (and handshake messages) in and out with websocket on top of it.  
Our goal is to use websocket framework to synchronically encode and decode websocket data without asio.  
And we managed to do it with boost 1.68.  
Here is our test code:  
```  
#include <boost/beast/core.hpp>  
#include <boost/beast/websocket.hpp>  
#include <boost/asio/connect.hpp>  
#include <cstdlib>  
#include <iostream>  
#include <string>  
  
namespace websocket = boost::beast::websocket;  // from <boost/beast/websocket.hpp>  
  
namespace NGtest {  
    class SyncStream;  
}  
  
class NGtest::SyncStream {  
public:  
    SyncStream() = default;  
  
    template <typename ConstBufferSequence>  
    size_t write_some(ConstBufferSequence const& buffers) {  
        boost::beast::error_code ec;  
        auto size = write_some(buffers, ec);  
        if (ec)  
            BOOST_THROW_EXCEPTION(boost::system::system_error{ec});  
        return size;  
    }  
  
    template <typename ConstBufferSequence>  
    size_t write_some(ConstBufferSequence const& buffers, boost::beast::error_code &ec) {  
        size_t size = 0;  
        for (auto && buffer : buffers) {  
            size += buffer.size();  
            std::copy(static_cast<const char *>(buffer.data()), static_cast<const char *>(buffer.data()) + buffer.size(), std::ostream_iterator<char>(std::cout));  
        }  
        std::cout.flush();  
        return size;  
    }  
  
    template <typename MutableBufferSequence>  
    size_t read_some(MutableBufferSequence const& buffers) {  
        boost::beast::error_code ec;  
        auto size = read_some(buffers, ec);  
        if (ec)  
            BOOST_THROW_EXCEPTION(boost::system::system_error{ec});  
        return size;  
    }  
  
    template <typename MutableBufferSequence>  
    size_t read_some(MutableBufferSequence const& buffers, boost::beast::error_code &ec) {  
        size_t size = 0;  
        for (auto && buffer : buffers) {  
            auto end = std::copy_n(std::istream_iterator<char>(std::cin), buffer.size(), static_cast<char *>(buffer.data()));  
            auto copied = end - static_cast<char *>(buffer.data());  
            size += copied;  
            if (copied < buffer.size()) {  
                return size;  
            }  
        }  
        return size;  
    }  
  
    typedef SyncStream executor_type;  
};  
  
template<>  
void boost::beast::websocket::teardown<NGtest::SyncStream>(role_type role, NGtest::SyncStream& socket, error_code& ec) {  
  
}  
  
// Sends a WebSocket message and prints the response  
int main(int argc, char** argv) {  
    try {  
        auto const host = "Host";  
        auto const text = "Hhhhh";  
  
        websocket::stream<NGtest::SyncStream> ws{};  
  
        // Perform the websocket handshake  
        ws.handshake(host, "/some");  
  
        // Send the message  
        ws.write(boost::asio::buffer(std::string(text)));  
  
        // This buffer will hold the incoming message  
        boost::beast::multi_buffer b;  
  
        // Read a message into our buffer  
        ws.read(b);  
  
        // Close the WebSocket connection  
        ws.close(websocket::close_code::normal);  
  
        // If we get here then the connection is closed gracefully  
  
        // The buffers() function helps print a ConstBufferSequence  
        std::cerr << boost::beast::buffers(b.data()) << std::endl;  
    }  
    catch(std::exception const& e)  
    {  
        std::cerr << "Error: " << e.what() << std::endl;  
        return EXIT_FAILURE;  
    }  
    return EXIT_SUCCESS;  
}  
```  
  
But with upgrade to boost 1.72 (and 1.71 also) we run into executor dependency for SyncStream.  
We are able to compile with addition of get_executor() and context() stubs, but it not just needed to be there, it really use this stubs:  
```  
#include <boost/beast/core.hpp>  
#include <boost/beast/websocket.hpp>  
#include <boost/asio/connect.hpp>  
#include <cstdlib>  
#include <iostream>  
#include <string>  
#include <exception>  
  
namespace websocket = boost::beast::websocket;  // from <boost/beast/websocket.hpp>  
  
namespace NGtest {  
    class SyncStream;  
}  
  
class NGtest::SyncStream {  
public:  
    SyncStream() = default;  
  
    template <typename ConstBufferSequence>  
    size_t write_some(ConstBufferSequence const& buffers) {  
        boost::beast::error_code ec;  
        auto size = write_some(buffers, ec);  
        if (ec)  
            BOOST_THROW_EXCEPTION(boost::system::system_error{ec});  
        return size;  
    }  
  
    template <typename ConstBufferSequence>  
    size_t write_some(ConstBufferSequence const& buffers, boost::beast::error_code &ec) {  
        size_t size = 0;  
        for (auto && buffer : buffers) {  
            size += buffer.size();  
            std::copy(static_cast<const char *>(buffer.data()), static_cast<const char *>(buffer.data()) + buffer.size(), std::ostream_iterator<char>(std::cout));  
        }  
        std::cout.flush();  
        return size;  
    }  
  
    template <typename MutableBufferSequence>  
    size_t read_some(MutableBufferSequence const& buffers) {  
        boost::beast::error_code ec;  
        auto size = read_some(buffers, ec);  
        if (ec)  
            BOOST_THROW_EXCEPTION(boost::system::system_error{ec});  
        return size;  
    }  
  
    template <typename MutableBufferSequence>  
    size_t read_some(MutableBufferSequence const& buffers, boost::beast::error_code &ec) {  
        size_t size = 0;  
        for (auto it = buffers.begin(); it != buffers.end(); ++it) {  
            auto && buffer = *it;  
            auto end = std::copy_n(std::istream_iterator<char>(std::cin), buffer.size(), static_cast<char *>(buffer.data()));  
            auto copied = end - static_cast<char *>(buffer.data());  
            size += copied;  
            if (copied < buffer.size()) {  
                return size;  
            }  
        }  
        return size;  
    }  
  
    size_t read_some(boost::asio::mutable_buffer const& buffer, boost::beast::error_code &ec) {  
        size_t size = 0;  
        auto end = std::copy_n(std::istream_iterator<char>(std::cin), buffer.size(), static_cast<char *>(buffer.data()));  
        auto copied = end - static_cast<char *>(buffer.data());  
        size += copied;  
        return size;  
    }  
  
    typedef boost::asio::executor executor_type;  
  
    executor_type& get_executor() {  
        throw std::runtime_error("Oh my god");  
        return *static_cast<executor_type*>(nullptr);  
    }  
  
    boost::asio::execution_context& context() {  
        throw std::runtime_error("Oh my god");  
        return *static_cast<boost::asio::execution_context*>(nullptr);  
    }  
};  
  
template<>  
void boost::beast::websocket::teardown<NGtest::SyncStream>(role_type role, NGtest::SyncStream& socket, error_code& ec) {  
  
}  
  
// Sends a WebSocket message and prints the response  
int main(int argc, char** argv) {  
    try {  
        auto const host = "Host";  
        auto const text = "Hhhhh";  
  
        websocket::stream<NGtest::SyncStream> ws{};  
  
        // Perform the websocket handshake  
        ws.handshake(host, "/some");  
  
        // Send the message  
        ws.write(boost::asio::buffer(std::string(text)));  
  
        // This buffer will hold the incoming message  
        boost::beast::multi_buffer b;  
  
        // Read a message into our buffer  
        ws.read(b);  
  
        // Close the WebSocket connection  
        ws.close(websocket::close_code::normal);  
  
        // If we get here then the connection is closed gracefully  
  
        // The buffers() function helps print a ConstBufferSequence  
        // std::cerr << boost::beast::buffers(b.data()) << std::endl;  
    }  
    catch(std::exception const& e)  
    {  
        std::cerr << "Error: " << e.what() << std::endl;  
        return EXIT_FAILURE;  
    }  
    return EXIT_SUCCESS;  
}  
```  
It is still possible to use websocket synchronically without being dependent on asio presence?

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-03-17 23:02:33 UTC  
> Url: https://github.com/boostorg/beast/issues/1878#issuecomment-600341821  

You seem to have written your own stream type which which writes data to a file descriptor (stdin/out)?  
But this already exists in asio. Out of interest, why are you looking to avoid asio?  
  
https://www.boost.org/doc/libs/1_72_0/doc/html/boost_asio/reference/posix__stream_descriptor.html  
  
https://www.boost.org/doc/libs/1_72_0/doc/html/boost_asio/reference/windows__basic_stream_handle.html

---

## Comment 2

> Username: madmongo1  
> Created at: 2020-03-17 23:11:46 UTC  
> Url: https://github.com/boostorg/beast/issues/1878#issuecomment-600344316  

Anyhow, the dependency on `get_executor` comes from the fact that `websocket::stream` models _AsyncReadStream_ and _AsyncWriteStream_, which both require `get_executor()` to exist.

---

## Comment 3

> Username: shuras109  
> Created at: 2020-03-18 00:00:43 UTC  
> Url: https://github.com/boostorg/beast/issues/1878#issuecomment-600357545  

For now it is just a test code in which we just use stdin and stdout. We are looking for a way to use beast websocket in environment where sockets running by its own eventloop.

---

## Comment 4

> Username: shuras109  
> Created at: 2020-03-18 00:06:42 UTC  
> Url: https://github.com/boostorg/beast/issues/1878#issuecomment-600359016  

So the only way is to use old version of boost without such dependency?

---

## Comment 5

> Username: vinniefalco  
> Created at: 2020-03-18 00:41:22 UTC  
> Url: https://github.com/boostorg/beast/issues/1878#issuecomment-600367561  

You have to replicate Asio's Executor machinery if you want it to work. Timers too.

---

## Comment 6

> Username: madmongo1  
> Created at: 2020-03-18 09:07:30 UTC  
> Url: https://github.com/boostorg/beast/issues/1878#issuecomment-600504809  

Note: ASIO already has machinery to allow interfacing with other event loops:  
  
https://www.boost.org/doc/libs/1_72_0/doc/html/boost_asio/overview/core/reactor.html

---

## Comment 7

> Username: shuras109  
> Created at: 2020-03-18 10:36:26 UTC  
> Url: https://github.com/boostorg/beast/issues/1878#issuecomment-600546855  

Thank you for directions for me! I will try figure a way out of this.

---

## Comment 8

> Username: madmongo1  
> Created at: 2020-03-18 10:46:16 UTC  
> Url: https://github.com/boostorg/beast/issues/1878#issuecomment-600551427  

Ah yes I had forgotten. Websocket streams now include timers for the automatic ping/pong.  
  
Asio is absolutely baked in

---

## Comment 9

> Username: vinniefalco  
> Created at: 2020-03-18 13:21:02 UTC  
> Url: https://github.com/boostorg/beast/issues/1878#issuecomment-600618757  

>We are looking for a way to use beast websocket in environment where sockets running by its own eventloop.  
  
This is possible and has been done before, it has to be done within the asio framework but you provide an implementation of the event loop yourself. Perhaps ask the asio author for pointers or read through the open and closed issues in that repo?

---

## Comment 10

> Username: shuras109  
> Created at: 2020-03-18 14:41:33 UTC  
> Url: https://github.com/boostorg/beast/issues/1878#issuecomment-600662346  

Thanks a lot!
