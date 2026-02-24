# #2880 - Error in Websocket client async_write handler (transferred length data) [Closed]

> Username: IvanShipaev  
> Created at: 2024-06-04 04:47:57 UTC  
> Updated at: 2024-06-06 14:28:27 UTC  
> Closed at: 2024-06-06 14:28:27 UTC  
> Url: https://github.com/boostorg/beast/issues/2880  

Error in Websocket client async_write handler.  
In the callback async_write when closing the connection on the other side, sometimes it returns incorrect for the transferred length data (0xFFFFFFFFFFFFFFFFF8 or in the type int64_t = -8)  
I use boost version 1.84.0  
```c++  
    void do_write {  
        ...   
        ws_->async_write(  
            net::const_buffer(bufferTX_.data(), len),  
            [self = this->shared_from_this(), this](const beast::error_code &ec, std::size_t length) {  
                if (ec) {  
                    std::stringstream ss;  
                    ss << "Error write length " << length << ": " << ssize_t(length);  
                    return fin(ec, ss.str().data());  
                }  
                do_write();  
            });  
    }  
...  
    void fin(const beast::error_code &ec, const char *s)  
    {  
        printf("er[%s] mes[%s]\n", ec.message().data(), s);  
        ws_->async_close({}, [](const beast::error_code &ec) {});  
    }  
              
```   
In the logs it looks like this   
er[Connection reset by peer] mes[Error write length 18446744073709551608: -8]  
  
The documentation says that in case of an error the length should not exceed the buffer size.  
However, debugging showed that the error occurs somewhere in this place.  
File /usr/local/include/boost/beast/websocket/impl/write.hpp  (line 434)  
```c++  
                n = bytes_transferred - impl.wr_fb.size();  
                bytes_transferred+= n;  
                if(impl.check_stop_now(ec))  
                    goto upcall;  
  
```   
The size wr_fb = 8 and bytes_transferred = 0, after this code bytes_transferred = 0xFFFFFFFFFFFFFFFFF8 and then call upcall  
```c++  
    upcall:  
        impl.wr_block.unlock(this);  
        impl.op_close.maybe_invoke()  
            || impl.op_idle_ping.maybe_invoke()  
            || impl.op_rd.maybe_invoke()  
            || impl.op_ping.maybe_invoke();  
        this->complete(cont, ec, bytes_transferred_);  
    }  
```

---

## Comment 1

> Username: ashtum  
> Created at: 2024-06-04 05:52:10 UTC  
> Url: https://github.com/boostorg/beast/issues/2880#issuecomment-2146667117  

Thank you for reporting this.  
  
I can confirm this is a bug. `net::async_write` can complete with an error while transferring fewer bytes than `impl.wr_fb.size()`, which causes `n = bytes_transferred - impl.wr_fb.size();` to underflow.
