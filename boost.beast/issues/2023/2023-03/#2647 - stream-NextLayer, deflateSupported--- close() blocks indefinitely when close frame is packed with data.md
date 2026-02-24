# #2647 - stream<NextLayer, deflateSupported>:: close() blocks indefinitely when close frame is packed with data [Closed]

> Username: david-komarek  
> Created at: 2023-03-01 16:10:28 UTC  
> Updated at: 2023-10-28 16:34:15 UTC  
> Closed at: 2023-10-28 16:34:15 UTC  
> Url: https://github.com/boostorg/beast/issues/2647  

### Version of Beast  
boost/beast/version.hpp - 300  
  
### Steps necessary to reproduce the problem  
Unfortunately this behavior is dependent on timing.  
  
Basically there is problem that the `close` method on the `stream` is blocking indefinitely in some cases.  
  
When the `close` is called on the client side, it correctly sends the closing frame to the server, the server should send closing frame (as a response). If there is (on the server side) buffered some unprocessed message, the message is appended to the closing frame and it seems that the client's `close` method has a difficult time to handle this frame correctly (blocks indefinitely).  
  
The following pcap shows the communication:  
No. 4 - client handshake  
No. 6 - server handshake  
No. 8 - client close  
No. 10 - server close (with some unprocessed message)  
  
No. 12 - 14 TCP FIN packets sent when the process was killed.  
  
[blocked_close.tar.gz](https://github.com/boostorg/beast/files/10863137/blocked_close.tar.gz)  
  
### All relevant compiler information  
Compiler - gcc (Ubuntu 11.3.0-1ubuntu1~22.04) 11.3.0  
Flags: `-g -Wno-unused-command-line-argument -fPIC -Wall -Wextra -pedantic -fsanitize=address -fno-omit-frame-pointer -fno-common -fsanitize=undefined -std=c++20`

---

## Comment 1

> Username: vinniefalco  
> Created at: 2023-03-01 16:12:00 UTC  
> Url: https://github.com/boostorg/beast/issues/2647#issuecomment-1450415959  

`close` is synchronous, should you be using `async_close`?

---

## Comment 2

> Username: david-komarek  
> Created at: 2023-03-01 16:24:26 UTC  
> Updated at: 2023-03-01 16:25:51 UTC  
> Url: https://github.com/boostorg/beast/issues/2647#issuecomment-1450434980  

> `close` is synchronous, should you be using `async_close`?  
  
This doesn't change the issue, the `close` isn't working correctly. (Or am I missing something?)  
  
Note: In my use case I don't need asynchronous behavior. Actually I need to wait for the close and there is nothing to be done in the meantime (testing application based on the boost.beast).

---

## Comment 3

> Username: vinniefalco  
> Created at: 2023-03-01 16:52:34 UTC  
> Url: https://github.com/boostorg/beast/issues/2647#issuecomment-1450476537  

Well the synchronous algorithms do not have the same capabilities as the asynchronous ones, because they cannot both read and write at the same time.

---

## Comment 4

> Username: david-komarek  
> Created at: 2023-03-02 07:15:10 UTC  
> Url: https://github.com/boostorg/beast/issues/2647#issuecomment-1451400228  

I've tried the `async_close` instead of `close` and the situation is same. Even the `async_close` isn't able to receive close frame with the pending message.

---

## Comment 5

> Username: david-komarek  
> Created at: 2023-03-02 08:59:43 UTC  
> Url: https://github.com/boostorg/beast/issues/2647#issuecomment-1451519396  

I've checked the PCAP again and I made a mistake. The closing frame isn't even send from the server side, although there is `async_read` in the loop of the coroutine.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2023-03-02 17:51:06 UTC  
> Url: https://github.com/boostorg/beast/issues/2647#issuecomment-1452277048  

Are you mixing sync and async? Have you set the timeouts in the websocket::stream?

---

## Comment 7

> Username: david-komarek  
> Created at: 2023-03-03 06:17:37 UTC  
> Url: https://github.com/boostorg/beast/issues/2647#issuecomment-1453036702  

Yes the async is used in the production code and the sync is used in unit tests.  
  
No, I didn't set the timeouts as the tests are running on localhost.  
  
In the beginning of the next week, I'll prepare minimal example, where I'll demonstrate how the connection is established, how the async read and write are handled on the server side and how the close is called on the client side. (Unfortunately, I don't have a time today or over weekend).

---

## Comment 8

> Username: klemens-morgenstern  
> Created at: 2023-03-06 00:03:10 UTC  
> Url: https://github.com/boostorg/beast/issues/2647#issuecomment-1455253974  

I think `close` will block any ops on the websocket, which can deadlock if another op is ongoing. Mixing sync and async is undefined behavior, please make sure the io_context isn't running when you call `close`.

---

## Comment 9

> Username: david-komarek  
> Created at: 2023-04-21 06:38:17 UTC  
> Updated at: 2023-04-21 06:44:25 UTC  
> Url: https://github.com/boostorg/beast/issues/2647#issuecomment-1517339042  

I'm very sorry for delayed response, but here is the example which sometimes fail (with very low probability).  
  
```c++  
#include <boost/asio.hpp>  
#include <boost/beast.hpp>  
#include <boost/system/system_error.hpp>  
  
#include <memory>   // std::enable_shared_from_this<>, std::*_ptr<>  
#include <utility>  // std::move()  
#include <thread>   // std::jthread<>, std::this_thread::sleep_for()  
#include <vector>   // std::vector<>  
#include <iostream> // std::cout, std::endl  
#include <chrono>   // std::chrono_literals::s  
  
  
namespace net       = boost::asio;  
namespace beast     = boost::beast;  
namespace websocket = beast::websocket;  
  
  
class connection : public std::enable_shared_from_this<connection>  
{  
public:  
    using websocket_stream = websocket::stream<beast::tcp_stream>;  
  
    static void create(websocket_stream websocket_)  
    {  
        std::shared_ptr<connection> ws(new connection(std::move(websocket_)));  
  
        ws->start_receiving();  
    }  
  
private:  
    explicit connection(websocket_stream websocket_)  
        : _websocket(std::move(websocket_))  
    {}  
  
    void start_receiving()  
    {  
        net::co_spawn(  
            _websocket.get_executor(),  
            [self = shared_from_this()]() { return self->reader(); },  
            net::detached);  
    }  
  
    net::awaitable<void> reader()  
    {  
        _websocket.text(true);  
        (void)co_await _websocket.async_write(net::buffer("some_data"), net::use_awaitable);  
  
        std::cout << "Reading" << std::endl;  
        while (_websocket.is_open())  
        {  
            // conntent skipped for the example purposes  
            beast::flat_buffer buffer;  
            try  
            {  
                (void)co_await _websocket.async_read(buffer, net::use_awaitable);  
            }  
            catch (const std::length_error &)  
            {  
                continue;  
            }  
            catch (const boost::system::system_error &e_)  
            {  
                if (e_.code() == websocket::error::closed)  
                {  
                    std::cout << "Closed" << std::endl;  
                    // connection closed  
                }  
                else  
                {  
                    // close connection on unknown error  
                    boost::system::error_code ec;  
                    _websocket.close(websocket::close_code::internal_error, ec);  
                }  
                break;  
            }  
            catch (...)  
            {  
                // close connection on unknown error  
                boost::system::error_code ec;  
                _websocket.close(websocket::close_code::internal_error, ec);  
                break;  
            }  
        }  
    }  
  
    websocket_stream _websocket;  
};  
  
  
net::awaitable<void> accept(net::io_context &io_)  
{  
    std::cout << "Accepting" << std::endl;  
  
    net::ip::tcp::acceptor acceptor(io_, { net::ip::tcp::v4(), 6666 });  
  
    try  
    {  
        connection::websocket_stream stream(co_await acceptor.async_accept(net::use_awaitable));  
        co_await stream.async_accept(net::use_awaitable);  
  
        std::cout << "Connection accepted" << std::endl;  
  
        connection::create(std::move(stream));  
    }  
    catch (const boost::system::system_error &e_)  
    {  
        // acceptor failed  
    }  
    catch (...)  
    {  
        // unknown error  
    }  
}  
  
  
int main(void)  
{  
    using namespace std::chrono_literals;  
  
    net::io_context io;  
  
    net::co_spawn(io, accept(io), net::detached);  
  
    std::vector<std::jthread> _executors;  
    net::executor_work_guard work(io.get_executor());  
  
    _executors.emplace_back([&io]() { io.run(); });  
    _executors.emplace_back([&io]() { io.run(); });  
  
    // just to make sure that the acceptor is up and ready  
    std::this_thread::sleep_for(1s);  
  
    connection::websocket_stream client_websocket(io);  
    auto address = net::ip::make_address("127.0.0.1");  
    client_websocket.next_layer().connect({ address, 6666 });  
    client_websocket.handshake(address.to_string() + ":" + std::to_string(6666), "/");  
  
    beast::flat_buffer buffer;  
    (void)client_websocket.read(buffer);  
  
    client_websocket.close(websocket::close_code::none);  
  
    return 0;  
}  
```  
  
```cmake  
cmake_minimum_required(VERSION 3.20)  
  
set(CMAKE_CXX_STANDARD 20)  
set(CMAKE_CXX_STANDARD_REQUIRED ON)  
set(CMAKE_CXX_EXTENSIONS OFF)  
  
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)  
  
  
project(websocket_example CXX)  
  
  
find_package(Boost 1.74 REQUIRED)  
  
  
list(APPEND EXAMPLE_FLAGS -Wall -Wextra -pedantic -Werror)  
  
  
add_executable(${PROJECT_NAME} example.cpp)  
target_compile_options(${PROJECT_NAME} PRIVATE ${EXAMPLE_FLAGS})  
target_link_libraries(${PROJECT_NAME} PRIVATE Boost::boost)  
```  
  
Boost 1.74 from debian repository  
GCC 11.3.0  
Ubuntu 22.04  
  
> Mixing sync and async is undefined behavior  
  
Where is this stated?  
  
> please make sure the io_context isn't running when you call close  
  
It must run as there is pending async read on the server side.

---

## Comment 10

> Username: ashtum  
> Created at: 2023-08-18 16:36:26 UTC  
> Url: https://github.com/boostorg/beast/issues/2647#issuecomment-1684165805  

@david-komarek I couldn't reproduce the described situation with the provided sample code, have tried with a different compiler?  
By the way, when using a multi-threaded executor, you should utilize asio::strand to avoid race conditions. An example can be found [here](https://github.com/boostorg/beast/blob/develop/example/websocket/server/async/websocket_server_async.cpp#L222).
