# #1035 - websocket::stream does not support non-blocking mode [Closed]

> Username: erosh  
> Created at: 2018-02-21 03:16:12 UTC  
> Updated at: 2018-02-22 00:54:29 UTC  
> Closed at: 2018-02-22 00:54:29 UTC  
> Url: https://github.com/boostorg/beast/issues/1035  

Hi.  
sorry for my poor English.  
I have beast version 144.  
I have my own ioloop in separate thread. I subscribe websocket native_hande for  EPOLLIN event using epoll_ctl.  
Everything is ok until server sends me a ping message.  
EPOLLIN event is triggered and I'm trying to read from the websocket.  
There is only ping message inside.  
But my application stucks inside   
...boost/beast/websocket/impl/read.ipp (line 989) loop  
what's for goto loop;(line 1043) after ping message handling?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-02-21 03:32:37 UTC  
> Updated at: 2018-02-21 03:34:02 UTC  
> Url: https://github.com/boostorg/beast/issues/1035#issuecomment-367202972  

`stream::async_read` only returns after a complete message has been read. Ping is not a message, it is a control frame (as are pongs and closes). Control frames are processed by Beast automatically, you won't normally see them. If you want to know when a ping is received, you need to register a function using `stream::control_callback`.  
  
Note the documentation for `stream::async_read`  
  
The asynchronous operation will continue until one of the following is true:  
  
* A complete message is received.  
* A close frame is received. In this case the error indicated by the function will be `websocket::error::closed`.  
* An error occurs on the stream.  
  
(http://www.boost.org/doc/libs/1_66_0/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/async_read.html)  
  
What behavior were you expecting?

---

## Comment 2

> Username: erosh  
> Created at: 2018-02-21 15:25:24 UTC  
> Url: https://github.com/boostorg/beast/issues/1035#issuecomment-367362334  

I don't want to use async_read.  
In such case I prefer non-blocking sync read.  
But as I understand it is not possible?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-02-21 15:45:47 UTC  
> Url: https://github.com/boostorg/beast/issues/1035#issuecomment-367369339  

non-blocking mode is not supported for `websocket::stream`. This should be noted in the documentation

---

## Comment 4

> Username: erosh  
> Created at: 2018-02-21 15:55:46 UTC  
> Updated at: 2018-02-21 15:58:25 UTC  
> Url: https://github.com/boostorg/beast/issues/1035#issuecomment-367372882  

I tried to inject my own basic_stream_socket with overloaded read_some functions.  
`template <typename MutableBufferSequence>  
  std::size_t read_some(const MutableBufferSequence &buffers,  
                        boost::system::error_code &ec) {  
    detail::buffer_sequence_adapter<boost::asio::mutable_buffer,  
                                    MutableBufferSequence>  
        bufs(buffers);`  
    `msghdr msg = msghdr();`  
    `msg.msg_iov = bufs.buffers();`  
    `msg.msg_iovlen = static_cast<int>(bufs.count());`  
    `ssize_t n = ::recvmsg(this->native_handle(), &msg, MSG_DONTWAIT);`  
    `if (n == 0) {`  
      `ec = boost::system::error_code(EPIPE, boost::system::system_category());`  
    `}`  
    `if (n == -1) {`  
     ` if (errno == EAGAIN || errno == EWOULDBLOCK || errno == EINTR) {`  
        `return 0;`  
      `}`  
     `ec = boost::system::error_code(errno, boost::system::system_category());`  
   ` }`  
    `return n;`  
  `}`  
  
It there is any chance to break this loop without rewriting almost everything?

---

## Comment 5

> Username: vinniefalco  
> Created at: 2018-02-21 16:07:34 UTC  
> Updated at: 2018-02-21 16:08:50 UTC  
> Url: https://github.com/boostorg/beast/issues/1035#issuecomment-367377112  

>It there is any chance to break this loop without rewriting almost everything?  
  
No. To support non-blocking mode, the synchronous operations (`read`, `write`, `ping`, and `close`) would need to save their state so that the code can be resumed from any operation which can return `EWOULDBLOCK`. Currently they don't do that, and modifying them to do so would require a significant refactoring. It might also not be possible to work non-blocking, note that `read` can cause both reads and writes on the socket. How would the caller know when to call `read` again because the write poll event is signaled?

---

## Comment 6

> Username: erosh  
> Created at: 2018-02-21 16:17:25 UTC  
> Url: https://github.com/boostorg/beast/issues/1035#issuecomment-367380733  

Ok. Thanks for your help.

---

## Comment 7

> Username: vinniefalco  
> Created at: 2018-02-21 16:45:31 UTC  
> Url: https://github.com/boostorg/beast/issues/1035#issuecomment-367390390  

I'll keep this open as a reminder to update the documentation, thanks!
