# #27 Add additional checks to handle ERROR_MORE_DATA [Closed]

> Username: Chaosvex  
> Created at: 2015-10-22 19:08:07 UTC  
> Updated at: 2017-12-02 07:49:39 UTC  
> Closed at: 2017-12-02 07:49:39 UTC  
> Url: https://github.com/boostorg/asio/pull/27  

When receiving from a UDP socket, passing a buffer smaller than the received datagram should trigger a boost::asio::error::message_size error. However, when Windows' IOCP is being used, the system error 0xEA (ERROR_MORE_DATA) is returned instead.   
  
A quick repro is to use Daytime.6 example. When built with IOCP and a datagram larger than a single byte is sent, the process will terminate rather than sending a response. If IOCP is disabled with BOOST_ASIO_DISABLE_IOCP, the error code will be set to boost::asio::error::message_size and a response will be sent. The expected behaviour is mentioned in the example's documentation, "Since we only provide the 1-byte recv_buffer_ to contain the client's request, the io_service object would return an error if the client sent anything larger. We can ignore such an error if it comes up."  
  
This patch maps ERROR_MORE_DATA to boost::asio::error::message_size when built with IOCP.  
  
Daytime.6: http://www.boost.org/doc/libs/1_59_0/doc/html/boost_asio/tutorial/tutdaytime6/src.html  
ERROR_MORE_DATA: https://msdn.microsoft.com/en-us/library/windows/desktop/ms681382%28v=vs.85%29.aspx

---

## Review 1 [Commented]

> Username: Calthron  
> Created_at: 2017-03-03 16:42:55 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/asio/pull/27#pullrequestreview-25016195  

I have just encountered this same issue the patch is intended to address.  Windows error code '234' or from winerror.h ERROR_MORE_DATA when the receive buffer for the UDP packet is not large enough to accommodate the data.  If not this or similar patch... what is the proper boost::asio way to handle the condition in the receive handler?

---

## Comment 2

> Username: chriskohlhoff  
> Created_at: 2017-12-02 07:49:39 UTC  
> Url: https://github.com/boostorg/asio/pull/27#issuecomment-348675558  

Fixed on develop by changing to use POSIX behaviour (i.e. no error on datagram truncation).

---
