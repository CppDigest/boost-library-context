# #2487 - websocket close and reconnect error [Closed]

> Username: ahao1995  
> Created at: 2022-07-26 15:31:49 UTC  
> Updated at: 2022-07-27 15:11:31 UTC  
> Closed at: 2022-07-27 15:11:31 UTC  
> Url: https://github.com/boostorg/beast/issues/2487  

```  
using websocket_stream = std::optional<websocket::stream<ssl::stream<tcp::socket>>>;  
class ws_session : public std::enable_shared_from_this<ws_session>  
{  
...    
private:  
    io_context        &ioc_;  
    ssl::context       ctx_;  
    websocket_stream   ws_stream_;  
};  
void ws_session::close()  
{  
    ioc_.post([this, self = shared_from_this()] {  
        boost::system::error_code ec;  
        ws_stream_->close(boost::beast::websocket::normal, ec);  
        ws_stream_->next_layer().shutdown(ec);  
        ws_stream_->next_layer().next_layer().close();  
    });  
}  
void ws_session::reset()  
{  
    if (ws_stream_)  
        close();  
    ws_stream_.emplace(ioc_, ctx_);  
}  
void ws_session::async_connect(ssl::verify_mode v_mode)  
{  
    reset();  
    tcp::resolver             resolver{ioc_};  
    boost::system::error_code ec;  
    auto const                results = resolver.resolve(host_, port_, ec);  
    if (ec) {  
        (connect_cb_)(ec);  
        return;  
    }  
    boost::asio::async_connect(  
    boost::beast::get_lowest_layer(*ws_stream_),  
    results,  
    std::bind(&ws_session::on_connect, shared_from_this(), v_mode, std::placeholders::_1, std::placeholders::_2));  
}  
```  
I want to close by myself and reconnect, so I called reset and async_connect, but there is an error (operation canceled)  
I want to know why,thanks

---

## Comment 1

> Username: madmongo1  
> Created at: 2022-07-26 16:45:01 UTC  
> Url: https://github.com/boostorg/beast/issues/2487#issuecomment-1195725545  

You’re mixing sync and async. Chaos incoming.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2022-07-26 18:38:21 UTC  
> Updated at: 2022-07-26 18:38:47 UTC  
> Url: https://github.com/boostorg/beast/issues/2487#issuecomment-1195846959  

![image](https://user-images.githubusercontent.com/1503976/181085650-f2e4b3f9-9522-46a9-8633-ca97836e91f0.png)

---

## Comment 3

> Username: ahao1995  
> Created at: 2022-07-27 01:02:01 UTC  
> Url: https://github.com/boostorg/beast/issues/2487#issuecomment-1196154902  

> You’re mixing sync and async. Chaos incoming.  
so, what should I do, any other way to close and reconnect? I canot find a demo to do this,thanks

---

## Comment 4

> Username: ahao1995  
> Created at: 2022-07-27 15:11:27 UTC  
> Url: https://github.com/boostorg/beast/issues/2487#issuecomment-1196888376  

> You’re mixing sync and async. Chaos incoming.  
  
I use async_close and then in callback to reconnect, it worked ,thank you
