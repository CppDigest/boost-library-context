# #2935 - Does boost::asio::post thread-safe ? [Closed]

> Username: xiaoma565  
> Created at: 2024-09-25 07:59:00 UTC  
> Updated at: 2024-10-16 08:23:08 UTC  
> Closed at: 2024-10-16 08:23:08 UTC  
> Url: https://github.com/boostorg/beast/issues/2935  

I used Apach JMeter to send request with 1000 threads.  
Then my beast server got a error "void boost::beast::detail::stream_base::pending_guard::assign(bool&): Assertion `! *b_' failed."  
  
In my code, I call `boost::asio::post` to call `async_read` and `async_write`. Is it valid?  In my ServerConnection, it has a public funcation name `SendRes`. It will be call by other threads. So I use `boost::asio::post`  to make my program thread-safe.  
  
```c++  
void ServerConnection::PostRead()  
{  
    boost::asio::post(stream_.get_executor(),  
        boost::beast::bind_front_handler(&ServerConnection::DoRead, shared_from_this()));  
}  
  
void ServerConnection::DoRead()  
{  
    auto self(shared_from_this());  
    req_ = {};  
    if (timeout_ > 0) {  
        stream_.expires_after(std::chrono::seconds(timeout_));  
    } else {  
        stream_.expires_at(boost::asio::steady_timer::time_point::max());  
    }  
    boost::beast::http::async_read(stream_, buffer_, req_, boost::beast::bind_front_handler(&ServerConnection::OnRead,  
        self));  
}  
```   
  
```c++  
void ServerConnection::PostSendRes()  
{  
    boost::asio::post(stream_.get_executor(),  
        boost::beast::bind_front_handler(&ServerConnection::OnSendRes, shared_from_this()));  
}  
  
void ServerConnection::SendRes(const ServerRes &res)  
{  
    if (isClose) {  
        return;  
    }  
    auto isBegin = resQueue.Empty();  
    resQueue.PushBack(ServerResInner(res));  
    if (isBegin) {  
        PostSendRes();  
    }  
}  
```   
  
[http_server.zip](https://github.com/user-attachments/files/17127358/http_server.zip)  
[error_log.txt](https://github.com/user-attachments/files/17127370/error_log.txt)

---

## Comment 1

> Username: ashtum  
> Created at: 2024-09-25 08:39:43 UTC  
> Url: https://github.com/boostorg/beast/issues/2935#issuecomment-2373437333  

Your `stream_` object should use its own `asio::strand` executor. You can see how it is constructed in this [example](https://github.com/boostorg/beast/blob/7ce0ebe47c5a99ecc0af6fd97f88c5f6bed7a8f8/example/http/server/async/http_server_async.cpp#L392).

---

## Comment 2

> Username: xiaoma565  
> Created at: 2024-09-25 11:22:14 UTC  
> Url: https://github.com/boostorg/beast/issues/2935#issuecomment-2373805799  

> Your `stream_` object should use its own `asio::strand` executor. You can see how it is constructed in this [example](https://github.com/boostorg/beast/blob/7ce0ebe47c5a99ecc0af6fd97f88c5f6bed7a8f8/example/http/server/async/http_server_async.cpp#L392).  
  
You means the ServerConnection's constructor is wrong?  
```c++  
ServerConnection::ServerConnection(boost::asio::io_context& ioContext, ServerHandler& serverHandler,  
    HttpServer* server) : isClose(false), server_(server), chunking(false), reqNum(0),  
    stream_(boost::asio::make_strand(ioContext)), serverHandler_(serverHandler), mTimer(ioContext) {}  
```   
  
Could you please give me a tip how to modify my code? This problem has bothered me for a long time.

---

## Comment 3

> Username: xiaoma565  
> Created at: 2024-09-25 11:37:35 UTC  
> Url: https://github.com/boostorg/beast/issues/2935#issuecomment-2373838222  

I create a ServerConnection variable form this file "HttpServer.cpp"  
```c++  
void HttpServer::DoAccept(uint32_t i, const TlsItems& tlsItems)  
{  
    if (count_ > maxConnections_) {  
        std::unique_lock<std::mutex> lock(mtx);  
        cv.wait(lock, [this] { return count_ <= maxConnections_; });  
    }  
    auto &ioContext = ioContextPool_.GetHttpIoCtx();  
    if (tlsItems.tlsEnable) {  
        std::shared_ptr<ServerConnection> newConnection = std::make_shared<SSLServerConnection>(ioContext,  
            serverHandler_[i], this, *(ctx[i]));  
        auto &tcpStream = boost::beast::get_lowest_layer(newConnection->GetSSLSocket());  
        acceptor_[i]->async_accept(tcpStream.socket(),  
            boost::beast::bind_front_handler(&HttpServer::HandleHandshake, this, newConnection, i, tlsItems));  
        LOG_D("[HttpServer] async accept success");  
    } else {  
        std::shared_ptr<ServerConnection> newConnection = std::make_shared<ServerConnection>(ioContext,  
            serverHandler_[i], this);  
        acceptor_[i]->async_accept(newConnection->GetSocket().socket(),  
            boost::beast::bind_front_handler(&HttpServer::HandleAccept, this, newConnection, i, tlsItems));  
    }  
}  
```   
Is there something wrong with my code?

---

## Comment 4

> Username: ashtum  
> Created at: 2024-09-25 14:11:09 UTC  
> Url: https://github.com/boostorg/beast/issues/2935#issuecomment-2374206547  

> > Your `stream_` object should use its own `asio::strand` executor. You can see how it is constructed in this [example](https://github.com/boostorg/beast/blob/7ce0ebe47c5a99ecc0af6fd97f88c5f6bed7a8f8/example/http/server/async/http_server_async.cpp#L392).  
>   
> You means the ServerConnection's constructor is wrong?  
>   
> ```c++  
> ServerConnection::ServerConnection(boost::asio::io_context& ioContext, ServerHandler& serverHandler,  
>     HttpServer* server) : isClose(false), server_(server), chunking(false), reqNum(0),  
>     stream_(boost::asio::make_strand(ioContext)), serverHandler_(serverHandler), mTimer(ioContext) {}  
> ```  
>   
> Could you please give me a tip how to modify my code? This problem has bothered me for a long time.  
  
It looks correct. Note that you can't schedule more than one `read` or `write` operation at a time; the previous operation must be completed before scheduling a new one.

---

## Comment 5

> Username: sehe  
> Created at: 2024-09-25 15:38:37 UTC  
> Updated at: 2024-09-25 15:39:05 UTC  
> Url: https://github.com/boostorg/beast/issues/2935#issuecomment-2374444058  

Could this be related to setting the stream timeout while a read/write operation is in progress? @xiaoma565  you can try commenting out the timeout-related calls to compare.
