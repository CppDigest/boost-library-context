# #2781 - Send data through a websocket connection from a separated thread [Closed]

> Username: zptan  
> Created at: 2023-12-24 14:59:39 UTC  
> Updated at: 2023-12-25 03:15:42 UTC  
> Closed at: 2023-12-25 03:15:41 UTC  
> Url: https://github.com/boostorg/beast/issues/2781  

Some info described in  
- https://github.com/boostorg/beast/issues/2780  
  
I need to send data through a websocket connection, but the method to send data may be called from any thread, I tried `boost::asio::dispatch()/post()` but doesn't work.  
  
IMHO, I just need to append a handler to the queue, and `io_context::run()` will execute it, just need to make it thread-safe, but it seems not so easy, 😢   
  
I have checked examples in https://www.boost.org/doc/libs/develop/libs/beast/example/websocket/, but I can't find solution.  
  
`WsConnection` which holds websocket connection  
```cc  
class WsConnection : public std::enable_shared_from_this<WsConnection> {  
   public:  
    using buffer           = boost::beast::flat_buffer;  
    ...  
    using ws_stream        = boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::tcp_stream>>;  
  
    WsConnection(  
        const Endpoint&            endpoint,  
        boost::asio::io_context&   io_ctx,  
        boost::asio::ssl::context& ssl_ctx)  
    : resolver_{boost::asio::make_strand(io_ctx)}  
    ...  
    , io_ctx_{io_ctx}  
    , ssl_ctx_{ssl_ctx} {}  
  
    virtual ~WsConnection() = default;  
  
    void Start() {  
        ws_ = std::make_unique<ws_stream>(boost::asio::make_strand(io_ctx_), ssl_ctx_);  
        ...  
    }  
  
    void Close();  
  
    void Write(std::string data, std::function<void(const error_code, const std::size_t)>&& on_write);  
  
    void Read(std::function<void(const error_code, buffer&)>&& on_read);  
  
    auto GetExecutor() { return ws_->get_executor(); }  
  
   protected:  
    void OnResolve(const error_code ec, const resolver_results& results);  
  
    void OnConnect(const error_code ec, const endpoint_type& endpoint);  
  
    void OnSSLHandshake(const error_code ec);  
  
    void OnHandshake(const error_code ec);  
  
    std::string_view HostInfo() const { return url_; }  
  
    resolver resolver_;  
    buffer   buf_;  
  
    const std::string url_;  
    const std::string host_;  
    const std::string target_;  
    const std::string port_;  
    const std::string host_port_;  
  
    std::unique_ptr<ws_stream> ws_;  
  
    boost::asio::io_context&   io_ctx_;  
    boost::asio::ssl::context& ssl_ctx_;  
};  
```  
  
And `WsSession` to send request though `WsConnection` instance  
```cc  
class WsSession : public std::enable_shared_from_this<WsSession> {  
   public:  
    ...  
  
    void SendRequest(std::shared_ptr<WsConnection> ws_conn) {  
        // This method may be called from a separated thread, so we need to make it thread-safe  
        // But it doesn't work, the handler hasn't been called  
        boost::asio::dispatch(ws_conn->GetExecutor(), [this, ws_conn, self = shared_from_this()] {  
        LOG(INFO) << ": Send data: " << payload_;  
        ws_conn->Write(  
            std::move(payload_),  
            [ws_conn, self](const WsConnection::error_code ec, const std::size_t) {  
                if (ec) {  
                    auto error{std::move(std::string{"Failed to write data, error: "}.append(ec.message()))};  
                    LOG(ERROR) << error;  
                    self->OnFail(std::move(error));  
                    return;  
                }  
  
                ws_conn->Read([self](const WsConnection::error_code ec, WsConnection::buffer& buf) {  
                    self->OnRead(ec, buf);  
                });  
            });  
    }  
  
   protected:  
    virtual void OnRead(const WsConnection::error_code ec, WsConnection::buffer& buf) = 0;  
  
    virtual void OnFail(std::string error) = 0;  
  
    std::string payload_;  
};  
```  
  
```cc  
int main() {  
    ...  
    boost::asio::io_context   io_ctx;  
    boost::asio::ssl::context ssl_ctx{boost::asio::ssl::context::tls_client};  
  
    auto ws_conn = std::make_shared<WsConnection>(endpoint, io_ctx, ssl_ctx);  
    ws_conn->Start();  
  
    std::thread t1{[&io_ctx] {  
        io_ctx.run();  
    }};  
  
    std::this_thread::sleep_for(std::chrono::seconds{3});  
  
    std::string payload{"Hello there!"};  
    auto        session = std::make_shared<Session>(std::move(payload));  
  
    // This doesn't work, the payload hasn't been sent  
    session->SendRequest(ws_conn);  
  
    t1.join();  
  
    return 0;  
}  
```  
  
The connection is successfully built, but fails to send data, can anyone help?

---

## Comment 1

> Username: zptan  
> Created at: 2023-12-24 15:21:35 UTC  
> Url: https://github.com/boostorg/beast/issues/2781#issuecomment-1868540656  

This works  
```cc  
  
    boost::asio::io_context   io_ctx;  
    boost::asio::ssl::context ssl_ctx{boost::asio::ssl::context::tls_client};  
  
    auto ws_conn = std::make_shared<WsConnection>(endpoint, io_ctx, ssl_ctx);  
    ws_conn->Start();  
  
    io_ctx.run();  
  
    io_ctx.restart();  
  
    std::string request{"Hello there!"};  
    auto        session = std::make_shared<WsSession>(std::move(request));  
  
    session->SendRequest(ws_conn);  
  
    io_ctx.run();  
```  
  
-----------------------------------------------------------------------  
  
But this doesn't work, `SendRequest()`'s handler called before handshake done, so error happens, can I just append the `SendRequest()` handler to the end of the queue?  
```cc  
auto ws_conn = std::make_shared<WsConnection>(endpoint, io_ctx, ssl_ctx);  
    ws_conn->Start();  
  
    std::atomic_bool stop{false};  
    std::thread t1{[&io_ctx, &stop] {  
        while (!stop) {  
            if (io_ctx.stopped()) {  
                io_ctx.restart();  
            }  
            io_ctx.run();  
        }  
    }};  
  
    std::this_thread::sleep_for(std::chrono::seconds{3});  
  
    std::string payload{"Hello there!"};  
    auto        session = std::make_shared<WsSession>(std::move(payload));  
  
    // This doesn't work, the payload hasn't been sent  
    session->SendRequest(ws_conn);  
  
    stop = true;  
    t1.join();  
```

---

## Comment 2

> Username: zptan  
> Created at: 2023-12-24 15:29:49 UTC  
> Url: https://github.com/boostorg/beast/issues/2781#issuecomment-1868542304  

This works!  
```cc  
...  
    auto ws_conn = std::make_shared<WsConnection>(endpoint, io_ctx, ssl_ctx);  
    ws_conn->Start();  
  
    std::atomic_bool stop{};  
    std::thread      t{[&io_ctx, &stop] {  
        while (!stop) {  
            if (io_ctx.stopped()) {  
                io_ctx.restart();  
            }  
            io_ctx.run();  
        }  
    }};  
  
    while (!ws_conn->Ready()) {  
        std::this_thread::sleep_for(std::chrono::seconds{1});  
    }  
  
    std::string request{"Hello, there!"};  
    auto        session = std::make_shared<WsSession>(std::move(request));  
  
    session->SendRequest(ws_conn);  
  
    stop = true;  
    t.join();  
```

---

## Comment 3

> Username: ashtum  
> Created at: 2023-12-24 17:52:23 UTC  
> Url: https://github.com/boostorg/beast/issues/2781#issuecomment-1868566777  

It is challenging to identify the problem in multi-threaded applications because many things can go wrong. Therefore, reading and reasoning about the code is not easy. What you are trying to achieve is entirely possible with a single-threaded application.  
Try running the code in debug mode and see to which line the handler executes.

---

## Comment 4

> Username: zptan  
> Created at: 2023-12-25 03:15:41 UTC  
> Url: https://github.com/boostorg/beast/issues/2781#issuecomment-1868693926  

@ashtum Thx for the advice.
