# #2873 - The problem of async_write_header or async_write [Closed]

> Username: xiaoma565  
> Created at: 2024-05-28 13:31:37 UTC  
> Updated at: 2024-05-29 11:42:10 UTC  
> Closed at: 2024-05-29 11:42:10 UTC  
> Url: https://github.com/boostorg/beast/issues/2873  

In my server, I send the header first, and send the body after.  
```c++  
void ServerConnection::SendNormalRes(boost::beast::http::status status, boost::beast::string_view str)  
{  
    auto self(shared_from_this());  
    res = {};  
    res.set(boost::beast::http::field::server, "Beast");  
    res.set(boost::beast::http::field::content_type, "text/plain");  
    res.version(version_);  
    res.keep_alive(keepAlive_); // true  
    res.result(status);  
    boost::beast::ostream(res.body()) << str;  
    sr_.emplace(res);  
    boost::beast::http::async_write_header(socket_, *sr_,  
        boost::beast::bind_front_handler(&ServerConnection::OnWriteHeader, self));  
}  
  
void ServerConnection::OnWriteHeader(boost::beast::error_code ec, size_t bytes_transferred)  
{  
    auto self(shared_from_this());  
    boost::ignore_unused(bytes_transferred);  
    if (ec) {  
        LOG("OnWrite error : %s\n", ec.what().c_str());  
        return DoClose();  
    }  
    boost::beast::http::async_write(socket_, *sr_,  
        boost::beast::bind_front_handler(&ServerConnection::OnWrite, self, keepAlive_));  
}  
  
void ServerConnection::OnWrite(bool keepAlive, boost::beast::error_code ec, size_t bytes_transferred)  
{  
    boost::ignore_unused(bytes_transferred);  
    if (ec) {  
        LOG("OnWrite error : %s\n", ec.what().c_str());  
        return DoClose();  
    }  
    if (sr_.has_value()) {  
        sr_.reset();  
    }  
    if (!keepAlive) {  
        return DoClose();  
    }  
    if (keepAlive_) {  
        DoRead();  
    }  
}  
```   
Why my client can't receive response immediately?  
  
When I exit the server, the response can be received.  
  
My client code:  
```c++  
void ClientConnection::DoReadHeader()  
{  
    auto self(shared_from_this());  
    p_.emplace();  
    boost::beast::http::async_read_header(socket_, buffer_, *p_,  
        boost::beast::bind_front_handler(&ClientConnection::OnReadHeader, self));  
}  
  
void ClientConnection::OnReadHeader(boost::beast::error_code ec, std::size_t bytes_transferred)  
{  
    boost::ignore_unused(bytes_transferred);  
    if(ec) {  
        LOG("OnReadHeader error : %s\n", ec.what().c_str());  
        return DoClose();  
    }  
      
    if (!p_->is_done()) {  
         boost::beast::http::async_read(socket_, buffer_, *p_,  
              boost::beast::bind_front_handler(&ClientConnection::OnReadBody, shared_from_this()));  
    }  
}  
  
void ClientConnection::OnReadBody(boost::beast::error_code ec, std::size_t bytes_transferred)  
{  
    if(ec) {  
        LOG("OnRead error : %s\n", ec.what().c_str());  
        return DoClose();  
    }  
  
    if (!p_->keep_alive()) {  
        return DoClose();  
    }  
    p_.reset();  
    DoReadHeader();  
}  
```

---

## Comment 1

> Username: xiaoma565  
> Created at: 2024-05-28 13:38:46 UTC  
> Url: https://github.com/boostorg/beast/issues/2873#issuecomment-2135244060  

OnReadHeader is invoked  
![image](https://github.com/boostorg/beast/assets/21019320/be9e1718-14ad-4699-a6f3-4f955e6369bf)  
  
OnReadBody seems to be blocked until I exit the server.

---

## Comment 2

> Username: ashtum  
> Created at: 2024-05-28 15:56:57 UTC  
> Url: https://github.com/boostorg/beast/issues/2873#issuecomment-2135601536  

Could you please provide two minimal reproducible examples for the client and server code?

---

## Comment 3

> Username: xiaoma565  
> Created at: 2024-05-29 11:42:04 UTC  
> Url: https://github.com/boostorg/beast/issues/2873#issuecomment-2137207368  

> Could you please provide two minimal reproducible examples for the client and server code?  
  
sorry，I forgot to use prepare_payload
