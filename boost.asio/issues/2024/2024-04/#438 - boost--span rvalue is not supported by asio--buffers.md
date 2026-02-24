# #438 - boost::span rvalue is not supported by asio::buffers [Open]

> Username: ujos  
> Created at: 2024-04-29 18:39:50 UTC  
> Updated at: 2024-11-13 23:27:42 UTC  
> Url: https://github.com/boostorg/asio/issues/438  

I have a function that returns a buffer as a span to read the data from socket:  
  
```cpp  
boost::span<std::byte> getRecvBuffer();  
```  
  
When I call `tcp::socket::async_receive(asio::buffer(getRecvBuffer()))`,   
  
```cpp  
socket_.async_receive(asio::buffer(getRecvBuffer()), [](auto&&...) {});  
```  
  
... I get an error:   
  
```  
error: no matching function for call to ‘boost::asio::mutable_buffer::mutable_buffer(const boost::asio::const_buffers_1&)’  
```  
  
The weird thing is that following code compiles:  
  
```cpp  
auto b1 = asio::buffer(getRecvBuffer());  
```  
  
Following code compiles too:  
  
```cpp  
auto buf = client.socketRecvBuffer_.recvBuffer();  
socket_.async_receive(asio::buffer(buf), [](auto&&...) {});  
```  
  
It seems in order to create a `mutable_buffer`, I need a lvalue for the buffer, which makes sense in case of `std::vector`, but it does not make sense for `boost::span`.

---

## Comment 1

> Username: sbhutch  
> Created at: 2024-11-13 22:26:51 UTC  
> Updated at: 2024-11-13 22:38:24 UTC  
> Url: https://github.com/boostorg/asio/issues/438#issuecomment-2475030765  

I believe I have come across a related issue (Boost 1.83) where the following code does not work:  
  
```cpp  
std::array buffers {  
    boost::asio::buffer(packet1.getPayload()),  
    boost::asio::buffer(packet2.getPayload()),  
};  
  
boost::asio::read(socket, buffers);  
```  
  
Where the `Packet::getPayload` signature is:  
  
```cpp  
class Packet {  
public:  
    boost::span<unsigned char> getPayload();  
};  
```  
  
Type deduction on the `std::array` is:  
  
```cpp  
std::array<boost::asio::const_buffers_1, 2UL> buffers  
```  
  
The `boost::asio::read` function fails due to the `const_buffers_1` deduction. However, if I provide the spans as an lvalue:  
  
```cpp  
auto buffer1 = packet1.getPayload();  
auto buffer2 = packet2.getPayload();  
  
std::array buffers {  
    boost::asio::buffer(buffer1),  
    boost::asio::buffer(buffer2),  
};  
  
boost::asio::read(socket, buffers);  
```  
  
Type deduction on the `std::array` becomes:  
  
```cpp  
std::array<boost::asio::mutable_buffers_1, 2UL> buffers  
```  
  
and all works well.
