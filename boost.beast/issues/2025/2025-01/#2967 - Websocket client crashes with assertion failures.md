# #2967 - Websocket client crashes with assertion failures [Closed]

> Username: ambbox  
> Created at: 2025-01-05 20:28:08 UTC  
> Updated at: 2025-01-06 17:24:00 UTC  
> Closed at: 2025-01-06 17:24:00 UTC  
> Url: https://github.com/boostorg/beast/issues/2967  

I am now able to reproduce an issue I reported before (which I thought I fixe)  
  
```C++  
#include <boost/asio.hpp>  
#include <boost/asio/awaitable.hpp>  
// #include <boost/asio/strand.hpp>  
#include <boost/beast/core.hpp>  
#include <boost/beast/websocket.hpp>  
#include <iostream>  
  
namespace asio = boost::asio;  
namespace beast = boost::beast;  
namespace websocket = beast::websocket;  
  
asio::io_context ioContext;  
  
class WebSocketClient {  
   public:  
    asio::ip::tcp::resolver resolver;  
    websocket::stream<beast::tcp_stream> ws;  
    std::future<void> readLoopFuture;  
  
   public:  
    WebSocketClient();  
    virtual ~WebSocketClient();  
    asio::awaitable<void> Connect();  
    void ReadLoop();  
    void Close();  
};  
  
WebSocketClient::WebSocketClient() : resolver(ioContext), ws(ioContext) {}  
  
WebSocketClient::~WebSocketClient() {  }  
  
asio::awaitable<void> WebSocketClient::Connect() {  
    std::string host = "localhost";  
    std::string port = "18649";  
    auto const results = co_await resolver.async_resolve(host, port, asio::use_awaitable);  
    co_await beast::get_lowest_layer(ws).async_connect(results, asio::use_awaitable);  
    co_await ws.async_handshake(host, "/", asio::use_awaitable);  
}  
  
void WebSocketClient::ReadLoop() {  
    readLoopFuture = asio::co_spawn(  
        ioContext,  
        [&]() -> asio::awaitable<void> {  
            co_await Connect();  
            std::cout << "Reading ";  
            try {  
                beast::flat_buffer buffer;  
                while (true) {  
                    co_await ws.async_read(buffer, asio::use_awaitable);  
                    std::cout << "1";  
                }  
            } catch (const std::exception& e) {  
                // std::cout << e.what() << std::endl;  
            }  
        },  
        asio::use_future);  
}  
  
void WebSocketClient::Close() {  
    asio::co_spawn(  
        ioContext,  
        [this]() -> asio::awaitable<void> {  
            try {  
                co_await ws.async_close(websocket::close_code::normal, asio::use_awaitable);  
            } catch (const std::exception& e) {  
                std::cout << "Close exception: " << e.what() << std::endl;  
            }  
        },  
        asio::use_future)  
        .get();  
    readLoopFuture.get();  
}  
  
int main(int argc, char** argv) {  
    asio::executor_work_guard<asio::io_context::executor_type> workGuard = asio::make_work_guard(ioContext);  
    std::vector<std::thread> threads;  
    for (int i = 0; i < 1; ++i) {  
        threads.emplace_back([&] { ioContext.run(); });  
    }  
    for (int i = 0; i < 10000; i++) {  
        WebSocketClient client;  
        std::cout << "\n==========" << i << "==========" << std::endl;  
        client.ReadLoop();  
        std::this_thread::sleep_for(std::chrono::milliseconds(50));  
        client.Close();  
    }  
  
    workGuard.reset();  
    for (auto& t : threads) {  
        t.join();  
    }  
  
    return 0;  
}  
```  
  
Server to test with:  
```Python  
import asyncio  
import websockets  
import argparse  
import ssl  
import logging  
  
logging.basicConfig(format="[%(name)s][%(asctime)s][%(levelname)s] %(message)s", level=logging.DEBUG)  
  
  
class TestServer:  
    def __init__(self):  
        self.stop_signal = asyncio.get_running_loop().create_future()  
        self.messages_task: asyncio.Task = None  
  
    def stop(self):  
        self.stop_signal.set_result(None)  
  
    async def start(self, ws_port, wss_port):  
        # ssl_context = ssl.SSLContext(ssl.PROTOCOL_TLS_SERVER)  
        # ssl_context.load_cert_chain(certfile="tests/test_cert.pem", keyfile="tests/test_key.pem")  
        async with websockets.serve(self.handler, "localhost", ws_port):  
            # async with websockets.serve(self.handler, "localhost", wss_port, ssl=ssl_context):  
            await self.stop_signal  
  
    async def handler(self, connection):  
        self.messages_task = asyncio.create_task(self.random_messages(connection))  
        try:  
            async for message in connection:  
                match message:  
                    case "close_me":  
                        break  
        except websockets.exceptions.ConnectionClosed:  
            pass  
  
    async def random_messages(self, connection):  
        try:  
            for i in range(1000):  
                await connection.send("1")  
                await asyncio.sleep(0.001)  
        except Exception:  
            pass  
  
  
async def main():  
    parser = argparse.ArgumentParser()  
    parser.add_argument("--ws-port", default=18649)  
    parser.add_argument("--wss-port", default=8649)  
    args = parser.parse_args()  
  
    server = TestServer()  
    await server.start(args.ws_port, args.wss_port)  
  
  
if __name__ == "__main__":  
    asyncio.run(main())  
  
  
```  
  
  
  
Output example:  
```  
...  
==========945==========  
Reading 1111111  
==========946==========  
TestClient: /root/.conan2/p/b/boost755dd9ecee15e/p/include/boost/beast/core/detail/stream_base.hpp:116: void boost::beast::detail::stream_base::pending_guard::assign(bool&): Assertion `! *b_' failed.  
Aborted (core dumped)  
```  
  
Another one (crashes in a different place):  
```  
...  
==========158==========  
TestClient: /root/.conan2/p/b/boost755dd9ecee15e/p/include/boost/beast/websocket/impl/close.hpp:260: void boost::beast::websocket::stream< <template-parameter-1-1>, <anonymous> >::close_op< <template-parameter-2-1> >::operator()(boost::beast::error_code, std::size_t, bool) [with Handler = boost::asio::detail::awaitable_handler<boost::asio::any_io_executor, boost::system::error_code>; NextLayer = boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy>; bool deflateSupported = true; boost::beast::error_code = boost::system::error_code; std::size_t = long unsigned int]: Assertion `impl.wr_block.is_locked(this)' failed.  
Aborted (core dumped)  
```

---

## Comment 1

> Username: ambbox  
> Created at: 2025-01-05 20:40:20 UTC  
> Updated at: 2025-01-05 20:41:01 UTC  
> Url: https://github.com/boostorg/beast/issues/2967#issuecomment-2571744955  

Maybe `Close` is called before `Connect` is done? Sounds unlikely be cause I had it reproduced with 100 ms sleep in between.

---

## Comment 2

> Username: ashtum  
> Created at: 2025-01-06 05:34:25 UTC  
> Url: https://github.com/boostorg/beast/issues/2967#issuecomment-2572310054  

There are multiple concurrency issues in your code. When using a multithreaded executor, ensure that you never access I/O objects (such as a resolver, socket, or `websocket::stream`) from multiple threads simultaneously. You can use an ASIO strand to achieve this:  
  
```C++  
#include <boost/asio.hpp>  
#include <boost/asio/awaitable.hpp>  
#include <boost/asio/strand.hpp>  
#include <boost/beast/core.hpp>  
#include <boost/beast/websocket.hpp>  
#include <iostream>  
  
namespace asio = boost::asio;  
namespace beast = boost::beast;  
namespace websocket = beast::websocket;  
  
class WebSocketClient {  
   public:  
    asio::ip::tcp::resolver resolver;  
    websocket::stream<beast::tcp_stream> ws;  
    std::future<void> readLoopFuture;  
  
   public:  
    WebSocketClient(asio::io_context&);  
    virtual ~WebSocketClient();  
    asio::awaitable<void> Connect();  
    void ReadLoop();  
    void Close();  
};  
  
WebSocketClient::WebSocketClient(asio::io_context& ioc)  
    : resolver(asio::make_strand(ioc))  
    , ws(resolver.get_executor()) // uses the same strand as resolver  
{  
}  
  
WebSocketClient::~WebSocketClient() {  }  
  
asio::awaitable<void> WebSocketClient::Connect() {  
    std::string host = "localhost";  
    std::string port = "18649";  
    auto const results = co_await resolver.async_resolve(host, port, asio::use_awaitable);  
    co_await beast::get_lowest_layer(ws).async_connect(results, asio::use_awaitable);  
    co_await ws.async_handshake(host, "/", asio::use_awaitable);  
}  
  
void WebSocketClient::ReadLoop() {  
    readLoopFuture = asio::co_spawn(  
        ws.get_executor(), // uses the same executor so any access inside the coroutine would be safe  
        [&]() -> asio::awaitable<void> {  
            co_await Connect();  
            std::cout << "Reading ";  
            try {  
                beast::flat_buffer buffer;  
                while (true) {  
                    co_await ws.async_read(buffer, asio::use_awaitable);  
                    std::cout << "1";  
                }  
            } catch (const std::exception& e) {  
                // std::cout << e.what() << std::endl;  
            }  
        },  
        asio::use_future);  
}  
  
void WebSocketClient::Close() {  
    asio::co_spawn(  
        ws.get_executor(), // uses the same executor so any access inside the coroutine would be safe  
        [this]() -> asio::awaitable<void> {  
            try {  
                co_await ws.async_close(websocket::close_code::normal, asio::use_awaitable);  
            } catch (const std::exception& e) {  
                std::cout << "Close exception: " << e.what() << std::endl;  
            }  
        },  
        asio::use_future)  
        .get();  
    readLoopFuture.get();  
}  
  
int main(int argc, char** argv) {  
    asio::io_context ioContext;  
    asio::executor_work_guard<asio::io_context::executor_type> workGuard = asio::make_work_guard(ioContext);  
    std::vector<std::thread> threads;  
    for (int i = 0; i < 1; ++i) {  
        threads.emplace_back([&] { ioContext.run(); });  
    }  
    for (int i = 0; i < 10000; i++) {  
        WebSocketClient client(ioContext);  
        std::cout << "\n==========" << i << "==========" << std::endl;  
        client.ReadLoop();  
        std::this_thread::sleep_for(std::chrono::milliseconds(50));  
        client.Close();  
    }  
  
    workGuard.reset();  
    for (auto& t : threads) {  
        t.join();  
    }  
  
    return 0;  
}  
  
```

---

## Comment 3

> Username: ambbox  
> Created at: 2025-01-06 13:45:13 UTC  
> Url: https://github.com/boostorg/beast/issues/2967#issuecomment-2573143146  

1. In the code I gave, `run` is only called from one thread.  
2. in this [example](https://www.boost.org/doc/libs/1_87_0/libs/beast/example/websocket/client/async/websocket_client_async.cpp), they use a different strand for the resolver. Which one is correct?

---

## Comment 4

> Username: ashtum  
> Created at: 2025-01-06 15:19:18 UTC  
> Updated at: 2025-01-06 15:19:34 UTC  
> Url: https://github.com/boostorg/beast/issues/2967#issuecomment-2573328705  

Here:  
```C++  
        client.ReadLoop();  
        std::this_thread::sleep_for(std::chrono::milliseconds(50));  
        client.Close();  
```  
  
You might initiate `async_close` operation while `async_handshake` is in progress. Initiating `async_close` while there is an ongoing `async_read` is fine but not `async_handshake`.  
  
> in this [example](https://www.boost.org/doc/libs/1_87_0/libs/beast/example/websocket/client/async/websocket_client_async.cpp), they use a different strand for the resolver. Which one is correct?  
  
Because operations on resolve and `websocket::stream` are in series both example are fine.
