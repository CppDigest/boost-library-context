# #271 - How to return from an udp blocking receive function? [Closed]

> Username: lidama  
> Created at: 2019-08-15 07:55:29 UTC  
> Updated at: 2020-12-30 01:08:16 UTC  
> Closed at: 2020-12-30 01:08:15 UTC  
> Url: https://github.com/boostorg/asio/issues/271  

There's a thread calling _boost::asio::ip::udp::socket::receive_from_ function in my program. When the program exit, I call _boost::asio::ip::udp::socket::close_ to make the blocking function _boost::asio::ip::udp::socket::receive_from_ return. This method works on Windows, but doesn't work on Linux.  
  
How can I resolve the problem?

---

## Comment 1

> Username: ghost  
> Created at: 2020-12-30 01:08:14 UTC  
> Url: https://github.com/boostorg/asio/issues/271#issuecomment-752292742  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#712](https://github.com/chriskohlhoff/asio/issues/712).
