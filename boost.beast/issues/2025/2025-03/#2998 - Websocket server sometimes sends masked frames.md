# #2998 - Websocket server sometimes sends masked frames [Closed]

> Username: cristianpetrache7  
> Created at: 2025-03-28 11:03:27 UTC  
> Updated at: 2025-05-10 19:56:04 UTC  
> Closed at: 2025-05-10 19:56:04 UTC  
> Url: https://github.com/boostorg/beast/issues/2998  

I have a websocket server implementation that I use in some unit tests to test my websocket client. Sometimes the tests are flaky and I see the message `The WebSocket frame was masked`. What could I have misconfigured so that the server sends masked frames at some point? I am using version 1.84.0.  
This behavior doesn't happen on an x86_64 architecture, but only on arm64. Has anyone else encountered this issue?  
Thank you!

---

## Comment 1

> Username: ashtum  
> Created at: 2025-03-28 11:46:47 UTC  
> Url: https://github.com/boostorg/beast/issues/2998#issuecomment-2761134901  

`The WebSocket frame was masked` error occurs when a client receives a masked WebSocket frame. Only the server should receive masked frames, as only the client is required to mask them.  
  
If you have flaky tests, there is a good chance you might have a memory corruption bug. Have you tried building your tests with memory and address sanitizers?

---

## Comment 2

> Username: cristianpetrache7  
> Created at: 2025-03-28 12:24:07 UTC  
> Url: https://github.com/boostorg/beast/issues/2998#issuecomment-2761215302  

Yes, I build with those sanitizers. Nothing is reported. Tests are flaky because the when the server sends a masked, the client closes the connection (which is normal behavior). But the expectations is my tests are that there is no reconnection happening.

---

## Comment 3

> Username: ashtum  
> Created at: 2025-03-28 12:28:08 UTC  
> Url: https://github.com/boostorg/beast/issues/2998#issuecomment-2761233470  

> But the expectations is my tests are that there is no reconnection happening.  
  
I couldn't understand this part. Do you mean that you expect an automatic reconnection?

---

## Comment 4

> Username: cristianpetrache7  
> Created at: 2025-03-28 12:31:57 UTC  
> Url: https://github.com/boostorg/beast/issues/2998#issuecomment-2761241549  

I have a test where I check that the messages from the server are correctly received by the client. In that test I expect that there is no reconnection happening. But since the server sends some masked frames at some point, the client closes the connection and then a reconnection is happening after the set period for reconnection is happening.

---

## Comment 5

> Username: ashtum  
> Created at: 2025-03-28 12:35:43 UTC  
> Url: https://github.com/boostorg/beast/issues/2998#issuecomment-2761249019  

Are both the server and the client using the Beast library? Could you reduce your test to minimal reproducible example?

---

## Comment 6

> Username: cristianpetrache7  
> Created at: 2025-03-28 13:56:34 UTC  
> Url: https://github.com/boostorg/beast/issues/2998#issuecomment-2761441464  

Yes, both are using the Beast library. Below you can find the minimal example for the server  
  
```c++  
#pragma once  
  
#include <boost/beast/core.hpp>  
#include <boost/beast/websocket.hpp>  
#include <memory>  
#include <string>  
#include <thread>  
  
class TestWsServer {  
 public:  
  TestWsServer(const std::string& ip, uint16_t port);  
  virtual ~TestWsServer();  
  TestWsServer(const TestWsServer&) = delete;  
  TestWsServer(TestWsServer&&) = delete;  
  TestWsServer& operator=(const TestWsServer&) = delete;  
  TestWsServer& operator=(TestWsServer&&) = delete;  
  
  void Run();  
  void CloseSession();  
  bool SendMessage(const std::string& message);  
  
 private:  
  std::string ip_;  
  uint16_t port_;  
  boost::asio::io_context io_{};  
  boost::asio::ip::tcp::acceptor acceptor_{io_};  
  boost::beast::flat_buffer buffer_{};  
  std::unique_ptr<std::thread> thread_;  
  std::unique_ptr<boost::beast::websocket::stream<boost::beast::tcp_stream>> ws_;  
  
  void DoAccept();  
  void OnAccept(boost::beast::error_code err, boost::asio::ip::tcp::socket socket);  
  void DoRead();  
  void OnRead(boost::beast::error_code err, std::size_t);  
};  
```  
```c++  
#include "test_ws_server.hpp"  
  
namespace beast = boost::beast;  
namespace websocket = beast::websocket;  
namespace net = boost::asio;  
using tcp = boost::asio::ip::tcp;  
using namespace std::chrono_literals;  
  
TestWsServer::TestWsServer(const std::string& ip, uint16_t port) : ip_(ip), port_(port) {}  
  
TestWsServer::~TestWsServer() {  
  if (!io_.stopped()) {  
    io_.stop();  
  }  
  
  if (thread_ && thread_->joinable()) {  
    thread_->join();  
  }  
  
  if (ws_) {  
    try {  
      if (ws_->is_open()) {  
        ws_->close(websocket::close_code::normal);  
      }  
    } catch (...) {  
      std::cerr << "Exception while closing the websocket." << std::endl;  
    }  
  }  
}  
  
void TestWsServer::Run() {  
  const tcp::endpoint endpoint{net::ip::make_address(ip_), port_};  
  acceptor_.open(endpoint.protocol());  
  acceptor_.set_option(net::socket_base::reuse_address(true));  
  acceptor_.bind(endpoint);  
  acceptor_.listen(net::socket_base::max_listen_connections);  
  
  DoAccept();  
  
  std::atomic_bool running = false;  
  thread_ = std::make_unique<std::thread>([this, &running] {  
    running = true;  
    io_.run();  
  });  
  
  while (!running) {  
    std::this_thread::sleep_for(1ms);  
  }  
}  
  
void TestWsServer::CloseSession() { ws_.reset(); }  
  
void TestWsServer::DoAccept() {  
  acceptor_.async_accept(io_, [this](beast::error_code err, tcp::socket socket) { OnAccept(err, std::move(socket)); });  
}  
  
void TestWsServer::OnAccept(beast::error_code err, tcp::socket socket) {  
  if (err) {  
    return;  
  }  
  ws_ = std::make_unique<websocket::stream<beast::tcp_stream>>(std::move(socket));  
  ws_->set_option(websocket::stream_base::timeout::suggested(beast::role_type::server));  
  ws_->async_accept([this](beast::error_code) { DoRead(); });  
  
  DoAccept();  
}  
  
void TestWsServer::DoRead() {  
  buffer_.consume(buffer_.size());  
  ws_->async_read(buffer_,  
                  [this](beast::error_code err, std::size_t bytes_transferred) { OnRead(err, bytes_transferred); });  
}  
  
bool TestWsServer::SendMessage(const std::string& message) {  
  boost::beast::flat_buffer write_buffer;  
  const auto message_size = message.size();  
  auto mutable_buf = write_buffer.prepare(message_size);  
  boost::asio::buffer_copy(mutable_buf, boost::asio::buffer(message));  
  write_buffer.commit(message_size);  
  
  try {  
    if (ws_) {  
      ws_->write(write_buffer.data());  
      return true;  
    }  
  
  } catch (const std::exception& ex) {  
    return false;  
  }  
  return false;  
}  
  
void TestWsServer::OnRead(beast::error_code err, std::size_t) {  
  if (err) {  
    return;  
  }  
  
  auto msg = beast::buffers_to_string(buffer_.data());  
  buffer_.consume(buffer_.size());  
  std::cout << "Got message: " << msg << std::endl;  
  DoRead();  
}  
```

---

## Comment 7

> Username: ashtum  
> Created at: 2025-03-29 10:30:48 UTC  
> Url: https://github.com/boostorg/beast/issues/2998#issuecomment-2763284378  

Your code has a concurrency bug in this function:  
```C++  
bool TestWsServer::SendMessage(const std::string& message) {  
  boost::beast::flat_buffer write_buffer;  
  const auto message_size = message.size();  
  auto mutable_buf = write_buffer.prepare(message_size);  
  boost::asio::buffer_copy(mutable_buf, boost::asio::buffer(message));  
  write_buffer.commit(message_size);  
  
  try {  
    if (ws_) {  
      ws_->write(write_buffer.data());  
      return true;  
    }  
  
  } catch (const std::exception& ex) {  
    return false;  
  }  
  return false;  
}  
```   
  
You can't access the WebSocket object directly from another thread; you need to post your task to the same executor that the WebSocket object is using. Additionally, using synchronous and asynchronous APIs simultaneously results in undefined behavior.  
  
I recommend taking a look at the [chat-multi](https://github.com/boostorg/beast/blob/develop/example/websocket/server/chat-multi) example for inspiration, especially this line: https://github.com/boostorg/beast/blob/develop/example/websocket/server/chat-multi/websocket_session.cpp#L84.

---

## Comment 8

> Username: ashtum  
> Created at: 2025-04-27 16:23:27 UTC  
> Url: https://github.com/boostorg/beast/issues/2998#issuecomment-2833534453  

@cristianpetrache7, Did this end up resolving your issue?
