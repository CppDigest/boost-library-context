# #178 - ubuntu: tcp socket is_open() and available() not working [Closed]

> Username: mwyborski  
> Created at: 2018-12-19 11:09:10 UTC  
> Updated at: 2018-12-20 14:07:55 UTC  
> Closed at: 2018-12-20 14:07:55 UTC  
> Url: https://github.com/boostorg/asio/issues/178  

I am having a hard time using boost asio with ubuntu. Several methods do not work:  
  
    socket.is_open()  
  
always returns "true" once it was connected, even if i unplug the lan cable and the connection definetly is not active any more, it keeps returning "true". I tested it with a USB-Lan-Adapter and disconnected it, it doesn't even recoginze that the adapter isn't present any more.  
  
I have set the **keep_alive** option, but it has no effect:  
  
    socket.set_option(boost::asio::socket_base::keep_alive(true));  
  
Also  
  
    socket.available()  
  
always returns 0, although there is data available to be read. Why would that be?   
Am i missing something here?

---

## Comment 1

> Username: djarek  
> Created at: 2018-12-20 02:09:24 UTC  
> Updated at: 2018-12-20 02:12:22 UTC  
> Url: https://github.com/boostorg/asio/issues/178#issuecomment-448832427  

@robotrovsky `socket.is_open()` only indicates whether the underlying socket descriptor is open. The underlying native socket descriptor isn't closed until you call `socket.close()` or move from the socket.  
  
As for `socket.available()` - it should work, I've just tested it (Ubuntu 18.04, Boost 1.69) and it returned `3` in my test code.

---

## Comment 2

> Username: mwyborski  
> Created at: 2018-12-20 14:07:55 UTC  
> Url: https://github.com/boostorg/asio/issues/178#issuecomment-449010350  

@djarek Thank you! I understand that socket.is_open() keeps returning true if the socket is not closed, but i would have expected it to notice, that it is running into a timeout due to keep_alive. And available() is always returning zero, maybe due to the blocking reading i do. I presume i would have to run the io_service to let it peek bytes in the socket.
