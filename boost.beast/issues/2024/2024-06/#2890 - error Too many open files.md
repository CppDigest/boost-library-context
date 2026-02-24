# #2890 - error Too many open files [Closed]

> Username: xiaoma565  
> Created at: 2024-06-21 09:11:59 UTC  
> Updated at: 2024-06-21 13:03:10 UTC  
> Closed at: 2024-06-21 13:03:10 UTC  
> Url: https://github.com/boostorg/beast/issues/2890  

My codes:  
```c++  
void HttpServer::HandleAccept(std::shared_ptr<ServerConnection> newConnection, const boost::system::error_code& ec)  
{  
    // Check whether the Server was stopped by a signal before this  
    // completion handler had a chance to run.  
    if (!acceptor_.is_open()) {  
        return;  
    }  
	if (ec) {  
        LOG("[HttpServer] connection accept failed, error is %s\n", ec.what().c_str());  
	} else {  
        newConnection->Start();  
        count_++;  
	}  
    if (count_ > maxConnections_) {  
        std::unique_lock<std::mutex> lock(mtx);  
        cv.wait(lock, [this] { return count_ <= maxConnections_; });  
    }  
	DoAccept();  
}  
  
void HttpServer::DoAccept()  
{  
    auto &ioContext = ioContextPool_.GetIoContext();  
    std::shared_ptr<ServerConnection> newConnection = std::make_shared<ServerConnection>(ioContext, reqHandler_, this);  
    acceptor_.async_accept(newConnection->GetSocket(), boost::beast::bind_front_handler(&HttpServer::HandleAccept, this,  
        newConnection));  
}  
```   
print so many error:  
![image](https://github.com/boostorg/beast/assets/21019320/bd2db91b-c8cc-4865-88a7-94d5701f6f48)  
  
In my server connection  
```c++  
void ServerConnection::OnRead(boost::beast::error_code ec, std::size_t bytes_transferred)  
{  
    boost::ignore_unused(bytes_transferred);  
  
    if(ec) {  
        LOG("[ServerConnection] OnRead error : %s\n", ec.what().c_str());  
        return DoClose();  
    }  
    keepAlive_ = req_.keep_alive();  
    version_ = req_.version();  
    auto fun = reqHandler_.GetFun(req_.method(), req_.target());  
    if (fun == nullptr) {  
        SendNormalRes(boost::beast::http::status::bad_request, "text/plain", "Invalid request\r\n");  
    } else {  
        fun(shared_from_this()); // 外部记得保存this的指针，防止引用计数到零后释放  
    }  
    if (keepAlive_) {  
        DoRead();  
    }  
}  
  
void ServerConnection::DoClose()  
{  
    isClose = true;  
    try {  
        socket_.shutdown(boost::asio::ip::tcp::socket::shutdown_send);  
    } catch (const std::exception& e) {  
        LOG("[ServerConnection] DoClose error : %s\n", e.what());  
    }  
    server_->CloseOne();  
}  
```   
Is that something wrong with "socket_.shutdown" ?

---

## Comment 1

> Username: xiaoma565  
> Created at: 2024-06-21 09:37:37 UTC  
> Url: https://github.com/boostorg/beast/issues/2890#issuecomment-2182393530  

should I call "socket.close" after "socket.shutdown"?  
```c++  
socket_.shutdown(boost::asio::ip::tcp::socket::shutdown_send);  
socket_.close();  
```

---

## Comment 2

> Username: ashtum  
> Created at: 2024-06-21 10:03:35 UTC  
> Url: https://github.com/boostorg/beast/issues/2890#issuecomment-2182434995  

It is hard to tell what could be wrong with the provided code sample. What happens inside `server_->CloseOne()`? How many active connections do you have? Do they exceed the `ulimit -n`?

---

## Comment 3

> Username: xiaoma565  
> Created at: 2024-06-21 10:55:08 UTC  
> Url: https://github.com/boostorg/beast/issues/2890#issuecomment-2182518879  

> It is hard to tell what could be wrong with the provided code sample. What happens inside `server_->CloseOne()`? How many active connections do you have? Do they exceed the `ulimit -n`?  
  
`server_->CloseOne()` code:  
```c++  
void HttpServer::CloseOne()  
{  
    if (count_ > maxConnections_) {  
        count_--;  
        cv.notify_one();  
    } else {  
        count_--;  
    }  
}  
```   
there are totally 1319 client connections, but i set single thread to accept.  
the ulimit -n is 1024.  
my io_contextpool is single thread too.  
So the process should look something like this:   accept -> do something -> close -> accept ...  
but why the socket still opening?  
I look at /proc/pid/fd with `ll | grep socket | wc -l` and print 1010.  
It seems that `socket.close()` did not work?

---

## Comment 4

> Username: ashtum  
> Created at: 2024-06-21 11:06:57 UTC  
> Url: https://github.com/boostorg/beast/issues/2890#issuecomment-2182538524  

> there are totally 1319 client connections, but i set single thread to accept. the ulimit -n is 1024. my io_contextpool is single thread too. So the process should look something like this: accept -> do something -> close -> accept ... but why the socket still opening? I look at /proc/pid/fd with `ll | grep socket | wc -l` and print 1010. It seems that `socket.close()` did not work?  
  
`socket.close()` should be sufficient. Is your application multi-threaded? What is the purpose of that condition variable, and how does it not block the call to `io_context.run()`? It is likely you are encountering a concurrency or lifetime bug. I suggest starting with a minimal single-threaded example and then extending it.

---

## Comment 5

> Username: xiaoma565  
> Created at: 2024-06-21 13:02:39 UTC  
> Url: https://github.com/boostorg/beast/issues/2890#issuecomment-2182713987  

> > there are totally 1319 client connections, but i set single thread to accept. the ulimit -n is 1024. my io_contextpool is single thread too. So the process should look something like this: accept -> do something -> close -> accept ... but why the socket still opening? I look at /proc/pid/fd with `ll | grep socket | wc -l` and print 1010. It seems that `socket.close()` did not work?  
>   
> `socket.close()` should be sufficient. Is your application multi-threaded? What is the purpose of that condition variable, and how does it not block the call to `io_context.run()`? It is likely you are encountering a concurrency or lifetime bug. I suggest starting with a minimal single-threaded example and then extending it.  
  
the keypoint  
```c++  
      headerCb = std::bind(&ClientConnection::ParserChunkHeader, self, std::placeholders::_1,  
            std::placeholders::_2, std::placeholders::_3);  
      bodyCb = std::bind(&ClientConnection::ParserChunkBody, self, std::placeholders::_1,  
          std::placeholders::_2, std::placeholders::_3);  
      p_->on_chunk_header(headerCb);  
      p_->on_chunk_body(bodyCb);  
```   
`headerCb` and `bodyCb` are ClientConnection's variables, they hold `self`.  
  
```c++  
void ClientConnection::Close()  
{  
    // Send a TCP shutdown  
    boost::beast::error_code ec;  
    socket_.shutdown(boost::asio::ip::tcp::socket::shutdown_both, ec);  
    // At this point the connection is closed gracefully  
    headerCb = nullptr;  
    bodyCb = nullptr;  
}  
```   
in funcation `Close()` we should set `headerCb` and `bodyCb` to nullptr otherwise the shared_ptr.use_count() will > 0.
