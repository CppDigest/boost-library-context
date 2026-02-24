# #4 - Can boost::mysql::tcp_connection work with boost::asio::async_connect ? [Closed]

> Username: MoeMod  
> Created at: 2020-05-08 13:59:48 UTC  
> Updated at: 2020-05-08 19:50:22 UTC  
> Closed at: 2020-05-08 19:50:22 UTC  
> Url: https://github.com/boostorg/mysql/issues/4  

After resolving a host, it gets several endpoints with boost::asio::ip::tcp::resolver::results_type. How to make boost::mysql::tcp_connection connect to them?

---

## Comment 1

> Username: anarthal  
> Created at: 2020-05-08 14:41:43 UTC  
> Url: https://github.com/boostorg/mysql/issues/4#issuecomment-625848772  

At this moment, it can only be used by explicitly connecting the underlying socket:  
```  
boost::asio::ip::tcp::resolver::results_type endpoints; // use the resolver to get these  
tcp_connection conn;  
conn.next_layer().connect(endpoints); // next_layer() returns the underlying stream, a boost::asio::ip::tcp::socket in your case  
conn.handshake(connection_params{/* username, password, database, SSL options... */});  
```  
(I have used sync functions for simplicity, but you can employ the async ones identically).  
  
However, your use case is completely legitimate and I plan to generalize `socket_connection::connect()` to accept any argument and forward it to the stream connect().  
  
Note that `socket_connection::connect()` performs a `shutdown()` and a `close()` of the underlying socket if handshake fails, closing the connection gracefully in case of error as dictated by MySQL protocol. You would have to do this manually atm.

---

## Comment 2

> Username: MoeMod  
> Created at: 2020-05-08 19:50:22 UTC  
> Url: https://github.com/boostorg/mysql/issues/4#issuecomment-625988893  

Thanks. Got that.
