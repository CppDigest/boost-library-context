# #749 - http_server_fast process request one by one [Closed]

> Username: maytrue  
> Created at: 2017-08-21 06:00:27 UTC  
> Updated at: 2017-08-23 04:12:47 UTC  
> Closed at: 2017-08-23 03:07:52 UTC  
> Url: https://github.com/boostorg/beast/issues/749  

I use demo http_server_fast as media server.  
  
Suppose there are two clients. Client one request video file and server return data, Client two need wait for sending complete( server to client one)  , then got data.  
  
How to modify demo to serve multi client simultaneously？  
  
Thanks!

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-08-21 13:54:36 UTC  
> Url: https://github.com/boostorg/beast/issues/749#issuecomment-323749380  

What do you mean? Separate connections are processed simultaneously. Why do you think that a client would have to wait for an unrelated connection to finish?

---

## Comment 2

> Username: maytrue  
> Created at: 2017-08-23 02:57:55 UTC  
> Url: https://github.com/boostorg/beast/issues/749#issuecomment-324207322  

I use argument "0.0.0.0 9000  ./path 3 spin".  
  
Client one send get request to http_server_fast, and http_server_fast send back a large file.  
Client two send get request to http_server_fast at the same and client two got connected until all data are sent to client one.  
  
Forgive my bad English!

---

## Comment 3

> Username: maytrue  
> Created at: 2017-08-23 03:03:12 UTC  
> Url: https://github.com/boostorg/beast/issues/749#issuecomment-324208021  

I am sorry, it is my fault.  
  
I modify   
`   http::async_write(  
                          socket_,  
                          *file_serializer_,  
                          [this](boost::beast::error_code ec)  
                          {  
                              socket_.shutdown(tcp::socket::shutdown_send, ec);  
                              file_serializer_.reset();  
                              file_response_.reset();  
                              accept();  
                          });`  
  
to   
`//        http::write(socket_, *file_serializer_, ec);  
//        socket_.shutdown(tcp::socket::shutdown_send, ec);  
//        file_serializer_.reset();  
//        file_response_.reset();  
//        file.close();  
//        accept();`

---

## Comment 4

> Username: vinniefalco  
> Created at: 2017-08-23 04:12:46 UTC  
> Url: https://github.com/boostorg/beast/issues/749#issuecomment-324216128  

Glad you figured it out!
