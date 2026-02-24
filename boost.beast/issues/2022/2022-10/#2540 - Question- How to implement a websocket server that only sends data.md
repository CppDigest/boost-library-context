# #2540 - Question: How to implement a websocket server that only sends data [Closed]

> Username: toutriste  
> Created at: 2022-10-12 02:02:38 UTC  
> Updated at: 2023-10-28 16:35:39 UTC  
> Closed at: 2023-10-28 16:35:39 UTC  
> Url: https://github.com/boostorg/beast/issues/2540  

### Version of Beast  
  
330  
  
### Problem  
  
Hi, maybe not a bug/issue, but I'm having some problems.  
My goal is to create a server where the main thread is looping and manipulating "signals" and a websocket server running on a separate thread, and when a signal "changes" the websocket has to annouce all the peers connected to it that signal changed.  
So basically, a "unidirectional" communication, the server does not need reading, only writing.  
  
This was my first implementation (briefly):  
```cpp  
// main thread (main.cpp)  
void on_signal_change_callback() {  
  // ...  
  events.push("test");  
}  
  
// websocket thread (wssv.cpp)  
while (1) {  
            auto& q = events; // queue  
            while (!q.empty()) {  
                auto changed = q.front();  
                q.pop();  
                ws.write(net::buffer(changed));  
            }  
            // PROBLEM: need to detect when client disconnects or this loop will run ..... forever?  
}  
```  
The problem here is that I cant detect when the client disconnects so I exit the for;; loop.. (not sure if there is any way of doing that?)  
  
The second implementation I tried was with async_x...  
  
```cpp  
void loop() {  
        ws.async_write(b.data(), [self{shared_from_this()}](beast::error_code ec, std::size_t bytes_sent) {  
            printf("async write!\n");  
            self->loop();  
        });  
}  
```  
  
The problem now is that after opening the connection, it writes once and then the connection is closed for some reason...  
(It also crashes:)  
```  
m: /usr/include/boost/beast/websocket/detail/soft_mutex.hpp:89: bool boost::beast::websocket::detail::soft_mutex::try_lock(const T*) [with T = boost::beast::websocket::stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy> >::read_some_op<boost::beast::websocket::stream<boost::beast::basic_stream<boost::asio::ip::tcp, boost::asio::any_io_executor, boost::beast::unlimited_rate_policy> >::read_op<CWebSocket::echo()::<lambda(boost::beast::error_code, std::size_t)>, boost::beast::basic_flat_buffer<std::allocator<char> > >, boost::asio::mutable_buffer>]: Assertion `id_ != T::id' failed.  
Aborted (core dumped)  
```  
  
**My question is, any idea on how I could implement a server that only writes to its peers when needed**

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2022-10-12 02:07:13 UTC  
> Url: https://github.com/boostorg/beast/issues/2540#issuecomment-1275491068  

it's not possibly, you need to continuously read, so that the internal state machine (i.e. handling control frames) gets pulled.  
  
If you need help with the assertion, I would need some more context, like who's calling `loop`.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2022-10-12 02:09:15 UTC  
> Url: https://github.com/boostorg/beast/issues/2540#issuecomment-1275492308  

I suggest you start by compiling and running one of the examples that does something close to what you want, and then modify it gradually to do what you need.

---

## Comment 3

> Username: paulbkeelp  
> Created at: 2022-12-29 23:24:11 UTC  
> Url: https://github.com/boostorg/beast/issues/2540#issuecomment-1367633299  

@toutriste any luck. I am trying the same for continous write to clients as broadcast messages . not able to figure out. any light ?

---

## Comment 4

> Username: vinniefalco  
> Created at: 2022-12-30 01:33:50 UTC  
> Url: https://github.com/boostorg/beast/issues/2540#issuecomment-1367676683  

You can't avoid reading. If any client wants to disconnect they will send a close frame, which you have to read.

---

## Comment 5

> Username: paulbkeelp  
> Created at: 2022-12-31 00:36:00 UTC  
> Updated at: 2022-12-31 00:38:02 UTC  
> Url: https://github.com/boostorg/beast/issues/2540#issuecomment-1368135031  

@vinniefalco . I see that chat-multi app RES and MEM is increasing slowly after running for some time when one client connected for write only and multiple clients are receving   
  
Top Command output  
At Start  
  
![image](https://user-images.githubusercontent.com/104987343/210119846-8bfabce0-b466-4ce0-bcf3-bd62f2a73b86.png)  
  
After Some time  
![image](https://user-images.githubusercontent.com/104987343/210119806-f6d16229-82d5-4d08-b05f-8be39b6678a1.png)

---

## Comment 6

> Username: ashtum  
> Created at: 2023-08-18 08:10:00 UTC  
> Url: https://github.com/boostorg/beast/issues/2540#issuecomment-1683533497  

@toutriste Could you please close the issue if your problem has been resolved?
