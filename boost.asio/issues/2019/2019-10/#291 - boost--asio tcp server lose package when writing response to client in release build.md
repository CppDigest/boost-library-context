# #291 - boost::asio tcp server lose package when writing response to client in release build [Closed]

> Username: smartseal  
> Created at: 2019-10-15 11:31:57 UTC  
> Updated at: 2020-12-30 01:10:13 UTC  
> Closed at: 2020-12-30 01:10:12 UTC  
> Url: https://github.com/boostorg/asio/issues/291  

hi,  
I make a server uing boost::asio async_write mode. boost 1.68 windows 32bit version developed with microsoft visualstudio 2017.  
  
When client first login to server, I write some previous result to the client for initiation.   
I async write these result using a boost::asio::streambuf, which is defined as class member for the socket session.  
  
In debug mode, everything is ok. But I change to release mode build, client only get a few response.   
  
On the client side, I debug the client, which got the async_read handler, but the readbuf is size 0.  
  
Is there any hint for this? Only happened in release build. Debug build works fine.

---

## Comment 1

> Username: ghost  
> Created at: 2020-12-30 01:10:11 UTC  
> Url: https://github.com/boostorg/asio/issues/291#issuecomment-752293044  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#724](https://github.com/chriskohlhoff/asio/issues/724).
