# #327 - Can I use MSG_ZEROCOPY with asio ? [Closed]

> Username: redboltz  
> Created at: 2020-02-28 06:22:53 UTC  
> Updated at: 2020-12-30 01:13:19 UTC  
> Closed at: 2020-12-30 01:13:18 UTC  
> Url: https://github.com/boostorg/asio/issues/327  

I tried to use MSG_ZEROCOPY with asio on Linux.  
The motivation that I tried it is avoiding a copy from user memory to kernel memory.  
  
Boost version 1.72.0  
Linux uname -a  
```  
Linux archboltz 5.5.5-arch1-1 #1 SMP PREEMPT Thu, 20 Feb 2020 18:23:09 +0000 x86_64 GNU/Linux.  
```  
  
According to the Linux document, in order to use MSG_ZEROCOPY, I need to set `SO_ZEROCOPY` option to the socket.   
  
https://www.kernel.org/doc/html/v4.15/networking/msg_zerocopy.html  
  
I think that I can do that using [basic_stream_socket::set_option](https://www.boost.org/doc/libs/1_72_0/doc/html/boost_asio/reference/basic_stream_socket/set_option.html).  
  
In addition, I need to set MSG_ZEROCOPY flag to `sendmsg()`.  
  
I've read the asio source then found the following code:  
  
https://github.com/boostorg/asio/blob/boost-1.72.0/include/boost/asio/basic_stream_socket.hpp#L858  
  
```cpp  
  template <typename ConstBufferSequence,  
      BOOST_ASIO_COMPLETION_TOKEN_FOR(void (boost::system::error_code,  
        std::size_t)) WriteHandler  
          BOOST_ASIO_DEFAULT_COMPLETION_TOKEN_TYPE(executor_type)>  
  BOOST_ASIO_INITFN_AUTO_RESULT_TYPE(WriteHandler,  
      void (boost::system::error_code, std::size_t))  
  async_write_some(const ConstBufferSequence& buffers,  
      BOOST_ASIO_MOVE_ARG(WriteHandler) handler  
        BOOST_ASIO_DEFAULT_COMPLETION_TOKEN(executor_type))  
  {  
    return async_initiate<WriteHandler,  
      void (boost::system::error_code, std::size_t)>(  
        initiate_async_send(this), handler,  
        buffers, socket_base::message_flags(0));  
  }  
```  
  
It seems that the flags is initialized as `socket_base::message_flags(0)`.  
  
Is there any way to use MSG_ZEROCOPY ?  
Or asio already achieved zero copy using different way (e.g. sendfile() system call) ?

---

## Comment 1

> Username: Oipo  
> Created at: 2020-06-20 15:22:46 UTC  
> Url: https://github.com/boostorg/asio/issues/327#issuecomment-647009114  

I'm not a boost developer, but I don't see MSG_ERRQUEUE pop up anywhere in boost asio, which is required for using MSG_ZEROCOPY.   
  
Moreover, all the memory given to asio to send should be kept in a buffer somewhere and not be touched until a completion event has returned.  
  
Given that most people would want zero copy on the receive side as well, asio would need to mmap/munmap a buffer for every receive request. Technically not very hard, but that might lead to a lot of contention, which is a known weak point for receive-side zero copy.  
  
It'll take some effort getting it into asio though.

---

## Comment 2

> Username: ghost  
> Created at: 2020-12-30 01:13:17 UTC  
> Url: https://github.com/boostorg/asio/issues/327#issuecomment-752293480  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#749](https://github.com/chriskohlhoff/asio/issues/749).
