# #2734 - SIGSEGV while calling `clear()` on `flat_static_buffer` [Closed]

> Username: rohith2506  
> Created at: 2023-09-05 09:15:29 UTC  
> Updated at: 2024-01-08 14:21:46 UTC  
> Closed at: 2024-01-08 14:21:46 UTC  
> Url: https://github.com/boostorg/beast/issues/2734  

Error message  
  
```  
Program terminated with signal SIGSEGV, Segmentation fault.  
#0  boost::beast::flat_static_buffer_base::clear (this=0x7f73e5ffa010) at /home/dev/blosh/build/vcpkg_installed/x64-linux/include/boost/beast/core/impl/flat_static_buffer.ipp:35  
```  
  
### Version of Beast  
  
BOOST_BEAST_VERSION 347 ( from `boost/beast/version.hpp` )  
  
### Steps necessary to reproduce the problem  
  
Hard to reproduce because this happens quite rare and I don't see any smoking guns  
  
### All relevant compiler information  
  
We use several optimisation flags while compiling this   
  
```  
cmake -DCMAKE_CXX_FLAGS="-O3 -march=native -mtune=native -flto -g"  
```  
  
### Info  
  
Here's the piece of code which calls `clear()`  
  
```  
void websocket_client::handle_data(const boost::system::error_code &, size_t, stream_context& stream_context) {  
    auto const buffer_data = stream_context.read_buffer_.data();  
    std::string payload(boost::asio::buffers_begin(buffer_data), boost::asio::buffers_end(buffer_data));  
    if (payload.empty()) { return ; }  
}  
  
  
  
void websocket_client::receive_data(stream_context& stream_context) {  
    stream_context.ssl_tcp_stream_.async_read(  
        stream_context.read_buffer_,  
        [&, this](const boost::system::error_code& error_code, std::size_t bytes_transferred) {  
            if (!error_code) {  
                handle_data(error_code, bytes_transferred, stream_context);  
                stream_context.read_buffer_.clear();  
                if (stream_context.runflag_) { receive_data(stream_context); }  
            }  
        }  
    );  
}  
```  
read_buffer:  `boost::beast::flat_static_buffer<4 * 1024 * 1024> read_buffer_{};`  
  
I looked into `clear()`  
  
```  
void  
flat_static_buffer_base::  
clear() noexcept  
{  
    in_ = begin_;  
    out_ = begin_;  
    last_ = begin_;  
}  
  
```  
  
seems like the it's just resetting the pointers so I was quite confused why this would cause SIGSEGV especially at the line `in_ = begin_`   
  
The only case I could think of is that if connection is destroyed and somehow it corrupted `flat_static_buffer`. To be honest, I don't have a clue yet. Wondering some of the experts here throw some light on this

---

## Comment 1

> Username: robotajet  
> Created at: 2023-09-05 09:24:57 UTC  
> Url: https://github.com/boostorg/beast/issues/2734#issuecomment-1706262112  

+1

---

## Comment 2

> Username: klemens-morgenstern  
> Created at: 2023-09-05 10:23:00 UTC  
> Url: https://github.com/boostorg/beast/issues/2734#issuecomment-1706349582  

That looks like a lifetime issue. I.e. your flat_static_buffer seems to go out of scope before the completion handler gets invoked. How do you keep the `stream_context` alive?

---

## Comment 3

> Username: rohith2506  
> Created at: 2023-09-05 10:41:15 UTC  
> Url: https://github.com/boostorg/beast/issues/2734#issuecomment-1706374810  

`stream_context` is stored in a vector the only time they go out of scope is that if the connection itself gets destroyed which I don't see happening here.   
  
And on top of that, if it's a lifetime issue, why it didn't complain at `handle_data` where I am reading from the buffer and constructing the payload.   
  
Maybe it has to do something with this?   
  
https://stackoverflow.com/questions/52236810/boost-beast-async-server-failing-with-assertion-failed-id-tid-on-multip  
  
my `async_read` completion handler may not keep up with streaming messages all the time

---

## Comment 4

> Username: klemens-morgenstern  
> Created at: 2023-09-05 10:59:53 UTC  
> Url: https://github.com/boostorg/beast/issues/2734#issuecomment-1706400940  

Idk. I reckon you can't reproduce that bug with an address sanitizer enabled?

---

## Comment 5

> Username: rohith2506  
> Created at: 2023-09-05 11:14:20 UTC  
> Url: https://github.com/boostorg/beast/issues/2734#issuecomment-1706421370  

yeah, that's my last straw considering this is the hot path in our system and hinders performance. But I guess I need to give that a shot

---

## Comment 6

> Username: ashtum  
> Created at: 2024-01-01 11:39:29 UTC  
> Url: https://github.com/boostorg/beast/issues/2734#issuecomment-1873289905  

@rohith2506 Did you manage to find the issue?
