# #2712 - HTTP parser seems always need EOM [Closed]

> Username: emmett2020  
> Created at: 2023-08-06 04:16:07 UTC  
> Updated at: 2024-01-02 14:29:13 UTC  
> Closed at: 2024-01-02 14:29:12 UTC  
> Url: https://github.com/boostorg/beast/issues/2712  

# Detail description.  
Hi, dear authors. Thanks for your awesome work. I have a question about implementation of `HTTP` parser.   
It seems that `beast` couldn't well parse the start line of `HTTP` request when the start line divided into two parts. In detail, If the client does not send a complete request line to the server, then the content sent to the server must end in `\r\n\r\n` to let server to continue parse. Let we see this example:  
  
In this example, client will send message to server twice times. And server is listening and dealing this client's request. The source code could be found below(nothing special, just a simply C/S code).   
![image](https://github.com/boostorg/beast/assets/59395781/a85ba115-2309-489c-8ae2-8348a4cbf9cd)  
![image](https://github.com/boostorg/beast/assets/59395781/a16d63a3-65c0-43fc-a3c9-e4b20d693c45)  
(The debug content of above image is I added to beast.)  
  
As a contrast, If the content which client  secondly send to server ended with EOM("\r\n\r\n"),  The server will succcessfully parse it.  
<img width="780" alt="image" src="https://github.com/boostorg/beast/assets/59395781/fe502f0b-4b13-4350-a39c-eaeb1958408c">  
<img width="499" alt="image" src="https://github.com/boostorg/beast/assets/59395781/21476c55-9db6-4c3f-b824-1aa9fdb9443f">  
  
I have carefully read the implementation. The below shows the relevant source code:  
![image](https://github.com/boostorg/beast/assets/59395781/b1a047e3-f58c-4d49-b267-53d9cd737711)  
![image](https://github.com/boostorg/beast/assets/59395781/166f3f52-cbad-4e85-be93-47f3adb2a6b3)  
  
I hope you can correct me if this is the expected behavior, or if I have a misunderstanding of the code.   
  
# Version of Beast  
```c++  
#define BOOST_BEAST_VERSION 300  
```  
  
# Steps necessary to reproduce the problem  
  
## 1. You can use this mock client :)  
Compile source code and run `./client  1080` command to start it.  
  
```c++  
#include <cstdlib>  
#include <cstring>  
#include <iostream>  
#include <utility>  
#include <boost/asio.hpp>  
  
using namespace std;         // NOLINT  
using namespace boost::asio; // NOLINT  
  
int main(int argc, char *argv[]) {  
  if (argc != 2) {  
    std::cerr << "Usage: client <port>\n";  
    return EXIT_FAILURE;  
  }  
  
  // 1. Connect to server.  
  boost::asio::io_context io_context;  
  boost::asio::ip::tcp::socket sock(io_context);  
  boost::asio::ip::tcp::resolver resolver(io_context);  
  boost::asio::connect(sock, resolver.resolve("127.0.0.1", argv[1]));  
  
  std::string buf;  
  size_t sz = 0;  
  
  // 2. Write an incomplete http request line.  
  buf.append("GET /index HT");  
  sz = write(sock, buffer(buf.data(), buf.size()));  
  std::this_thread::sleep_for(0.2s);  
  
  // 3. Write the left message to make the http request line completed.  
  buf.clear();  
  buf.append("TP/1.1\r\n");  
  sz = write(sock, buffer(buf.data(), buf.size()));  
  std::this_thread::sleep_for(2s);  
  
  return 0;  
}  
```  
  
## 2. You can use this mock server :)  
```c++  
#include <iostream>  
#include <thread> // NOLINT  
  
#include <boost/asio/ip/tcp.hpp>  
#include <boost/beast/core.hpp>  
#include <boost/beast/http.hpp>  
#include <boost/beast/version.hpp>  
#include <boost/config.hpp>  
  
void do_session(boost::asio::ip::tcp::socket &socket) { // NOLINT  
  boost::beast::error_code ec;  
  boost::beast::flat_buffer buffer;  
  
  for (;;) {  
    boost::beast::http::request<boost::beast::http::string_body> req;  
    boost::beast::http::read(socket, buffer, req, ec);  
    if (ec == boost::beast::http::error::end_of_stream) {  
      break;  
    } else if (ec) {  
      std::cout << "ERROR: " << ec.message() << std::endl;  
      break;  
    }  
  }  
  socket.shutdown(boost::asio::ip::tcp::socket::shutdown_send, ec);  
}  
  
int main() {  
  auto address = boost::asio::ip::make_address("127.0.0.1");  
  auto port = static_cast<ushort>(1080);  
  boost::asio::io_context ctx{1};  
  boost::asio::ip::tcp::acceptor acceptor{ctx, {address, port}};  
  for (;;) {  
    boost::asio::ip::tcp::socket socket{ctx};  
    acceptor.accept(socket);  
    std::jthread{std::bind(&do_session, std::move(socket))};  
  }  
}  
```  
  
# All relevant compiler information  
Each compiler is OK since it's not a compiler specific question.

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2023-08-06 05:29:28 UTC  
> Url: https://github.com/boostorg/beast/issues/2712#issuecomment-1666725662  

That'll need a closer look, I'll check it next week.

---

## Comment 2

> Username: emmett2020  
> Created at: 2023-08-06 11:22:29 UTC  
> Url: https://github.com/boostorg/beast/issues/2712#issuecomment-1666822085  

> That'll need a closer look, I'll check it next week.  
  
Thank you.

---

## Comment 3

> Username: klemens-morgenstern  
> Created at: 2023-08-08 01:45:02 UTC  
> Url: https://github.com/boostorg/beast/issues/2712#issuecomment-1668788354  

You are writing a partial message. YOu need to end the message with `\r\n\r\n`, i.e. change this  
  
```cpp  
  buf.append("TP/1.1\r\n");  
```  
  
to this:  
  
```cpp  
  buf.append("TP/1.1\r\n\r\n");  
```  
  
Then everything works.

---

## Comment 4

> Username: klemens-morgenstern  
> Created at: 2023-08-14 10:17:37 UTC  
> Url: https://github.com/boostorg/beast/issues/2712#issuecomment-1677059220  

@Runner-2019 did that solve it? Is this issue still open?

---

## Comment 5

> Username: emmett2020  
> Created at: 2023-08-14 15:46:45 UTC  
> Url: https://github.com/boostorg/beast/issues/2712#issuecomment-1677588686  

> @Runner-2019 did that solve it? Is this issue still open?  
  
Thanks for your help. I may need some time to carefully think what you have said. I will closed it these two days.

---

## Comment 6

> Username: ashtum  
> Created at: 2023-12-31 08:35:46 UTC  
> Url: https://github.com/boostorg/beast/issues/2712#issuecomment-1872857439  

@Runner-2019 Is this issue still open?

---

## Comment 7

> Username: emmett2020  
> Created at: 2024-01-02 14:29:12 UTC  
> Url: https://github.com/boostorg/beast/issues/2712#issuecomment-1874095837  

> @Runner-2019 Is this issue still open?  
  
Thanks for your help. I will close this issue.
