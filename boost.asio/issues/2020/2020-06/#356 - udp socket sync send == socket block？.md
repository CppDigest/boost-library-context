# #356 - udp socket sync send == socket block？ [Closed]

> Username: magicsupery  
> Created at: 2020-06-03 10:05:02 UTC  
> Updated at: 2020-12-30 01:14:28 UTC  
> Closed at: 2020-12-30 01:14:27 UTC  
> Url: https://github.com/boostorg/asio/issues/356  

boost::asio::ip::udp::socket socket;   
std::string a("afsfesfe");    
boost::system::error_code ec;  
socket.send(boost::asio::buffer(a,data(), a.size()), 0, ec);  
  
is the thread will block?  
if block, where is the block point?

---

## Comment 1

> Username: ghost  
> Created at: 2020-12-30 01:14:26 UTC  
> Url: https://github.com/boostorg/asio/issues/356#issuecomment-752293669  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#763](https://github.com/chriskohlhoff/asio/issues/763).
