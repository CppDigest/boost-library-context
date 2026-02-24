# #3006 - During the process of sending messages in a WebSocket client, errors often occur, such as "unspecified system error" and "sslv3 alert bad record mac." [Closed]

> Username: zhiweiwo  
> Created at: 2025-04-28 02:50:09 UTC  
> Updated at: 2025-04-29 01:53:41 UTC  
> Closed at: 2025-04-29 01:53:41 UTC  
> Url: https://github.com/boostorg/beast/issues/3006  

Could you please provide specific explanations for these error messages? I couldn't find them in the documentation.

---

## Comment 1

> Username: ashtum  
> Created at: 2025-04-28 05:01:25 UTC  
> Url: https://github.com/boostorg/beast/issues/3006#issuecomment-2833986914  

These errors are related to the underlying stream layer used with the `websocket::stream` object. When you initiate an operation, such as `websocket::stream::async_write`, any error that occurs in the underlying stream will cause the completion handler to be invoked with the corresponding error code.  
  
For example, if you are using `asio::ssl::stream<asio::ip::tcp::socket>` as the underlying stream layer, you should expect to encounter errors like:    
- [ssl::error::stream_errors](https://www.boost.org/doc/libs/develop/doc/html/boost_asio/reference/ssl__error__stream_errors.html)  
- [error::basic_errors](https://www.boost.org/doc/libs/develop/doc/html/boost_asio/reference/error__basic_errors.html)

---

## Comment 2

> Username: zhiweiwo  
> Created at: 2025-04-28 05:48:47 UTC  
> Url: https://github.com/boostorg/beast/issues/3006#issuecomment-2834047008  

please ask what might be the reasons for these mistakes?

---

## Comment 3

> Username: ashtum  
> Created at: 2025-04-28 06:01:46 UTC  
> Url: https://github.com/boostorg/beast/issues/3006#issuecomment-2834064543  

Could you please provide more details about your code? Are you using existing examples from Beast repository? Do these errors occur only with a specific host?  
By the way, these are generic errors, you can often find more information by searching the exact error message.

---

## Comment 4

> Username: zhiweiwo  
> Created at: 2025-04-28 06:03:13 UTC  
> Url: https://github.com/boostorg/beast/issues/3006#issuecomment-2834066779  

I'd like to add a bit of information.  I didn't load the certificate.  The error occurred during the process of the client sending data.  Does this mean it has nothing to do with certificate verification or the like, but is it related to network fluctuations

---

## Comment 5

> Username: zhiweiwo  
> Created at: 2025-04-28 06:04:46 UTC  
> Url: https://github.com/boostorg/beast/issues/3006#issuecomment-2834069270  

```  
        net::io_context ioc;  
        ssl::context ctx{ssl::context::tlsv12_client};  
        tcp::resolver resolver_ws{ioc};  
        std::unique_ptr<websocket::stream<beast::ssl_stream<beast::tcp_stream>>> ws;  
	ws = std::make_unique<websocket::stream<beast::ssl_stream<beast::tcp_stream>>>(ioc, ctx);  
        auto const results_ws = resolver_ws.resolve(host_.c_str(), port_.c_str(),ec);  
        if (ec)  
        {  
            cbf_->on_error(CZSL_CONNECT_FAIL,ec.message().c_str());  
            return CZSL_CONNECT_FAIL;  
        }  
        if (ws == nullptr)  
        {  
            return Status(CZSL_CONNECT_FAIL,"ws is nullptr");  
        }  
        beast::get_lowest_layer(*ws).connect(results_ws,ec);  
        if (ec)  
        {  
            cbf_->on_error(CZSL_CONNECT_FAIL,ec.message().c_str());  
            return CZSL_CONNECT_FAIL;  
        }  
        ws->next_layer().handshake(ssl::stream_base::client,ec);  
        if(ec)  
        {  
            cbf_->on_error(CZSL_CONNECT_FAIL,ec.message().c_str());  
            return CZSL_CONNECT_FAIL;  
        }  
        ws->handshake(host_.c_str(), ws_endpoint.c_str(), ec);  
        if (ec)  
        {  
            cbf_->on_error(CZSL_CONNECT_FAIL,ec.message().c_str());  
            return CZSL_CONNECT_FAIL;  
        }  
```  
This is the code I used in the connection stage. The sending adopts a synchronization mechanism

---

## Comment 6

> Username: ashtum  
> Created at: 2025-04-28 06:07:56 UTC  
> Url: https://github.com/boostorg/beast/issues/3006#issuecomment-2834074433  

Do these errors occur only when you are trying to connect to a specific server, or do they occur with any server?

---

## Comment 7

> Username: zhiweiwo  
> Created at: 2025-04-28 06:08:55 UTC  
> Url: https://github.com/boostorg/beast/issues/3006#issuecomment-2834075964  

I have only attempted to connect to one server

---

## Comment 8

> Username: ashtum  
> Created at: 2025-04-28 06:24:21 UTC  
> Url: https://github.com/boostorg/beast/issues/3006#issuecomment-2834102043  

You might also want to try another server to verify your code. It could be helpful to start with one of the existing client examples and modify it step by step.

---

## Comment 9

> Username: zhiweiwo  
> Created at: 2025-04-28 06:51:38 UTC  
> Url: https://github.com/boostorg/beast/issues/3006#issuecomment-2834153099  

Finally, I'd like to ask, if I keep reading in one thread and writing in another, is this approach compliant?

---

## Comment 10

> Username: ashtum  
> Created at: 2025-04-28 07:38:04 UTC  
> Url: https://github.com/boostorg/beast/issues/3006#issuecomment-2834255389  

No, you can't access the `websocket::stream` object from two threads. This restriction also applies to other I/O objects in Asio, such as `asio::ip::tcp::stream`. If you're using a multi-threaded executor, you need to wrap it in an `asio::strand` before passing it to the `websocket::stream`. Additionally, if you need to access the `websocket::stream` from another thread, you must post a function object to the same executor that was passed to the constructor. You can refer to this example to learn the pattern:  
https://github.com/boostorg/beast/blob/19c38c308a3aed59843459379ea042839b1193e2/example/websocket/server/chat-multi/websocket_session.cpp#L82-L96
