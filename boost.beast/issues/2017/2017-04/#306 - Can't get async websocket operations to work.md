# #306 - Can't get async websocket operations to work [Closed]

> Username: markvasiv  
> Created at: 2017-04-17 18:41:12 UTC  
> Updated at: 2017-04-18 11:55:35 UTC  
> Closed at: 2017-04-18 11:55:35 UTC  
> Url: https://github.com/boostorg/beast/issues/306  

I'm trying to build a WebSocket notification receiver using the beast library, however, I faced a problem with async operations. I would be very thankful if you could help me to figure out what's wrong here.  
  
Consider the following code:  
```cpp  
    template <class HandshakeF, class CompletionF>  
    WebSocketNotificationsReceiver(const std::string &host, const std::string &method, HandshakeF handshakeDecorator, CompletionF completionHandler)  
    {  
        boost::asio::io_service ios;  
        boost::asio::ip::tcp::resolver r{ios};  
        boost::asio::ip::tcp::socket sock{ios};  
        boost::asio::connect(sock, r.resolve(boost::asio::ip::tcp::resolver::query{host, "8089"}));  
        beast::websocket::stream<boost::asio::ip::tcp::socket&> ws(sock);  
  
        ws.async_handshake(host, method, [&](boost::system::error_code const& ec)  
        {  
            //some code  
        });  
    }  
```  
Nothing special here, however, the async_handshake handler will never be called. If I replace the async call with the sync one - everything will work correctly. The same happens with the async_read.

---

## Comment 1

> Username: mika-fischer  
> Created at: 2017-04-18 07:18:30 UTC  
> Url: https://github.com/boostorg/beast/issues/306#issuecomment-294710260  

You must ensure that `ios`, `sock` and `ws` stay alive until you don't need them anymore. The way it's written, they are destroyed right after async_handshake is called. Since that's an async operation, the call returns immediately, the objects are destroyed and hence the async operation is cancelled.

---

## Comment 2

> Username: markvasiv  
> Created at: 2017-04-18 09:50:26 UTC  
> Url: https://github.com/boostorg/beast/issues/306#issuecomment-294754318  

Thanks! That's what I actually thought. One more thing, what if I wrap sync operations in a thread? Is is considered an antipattern?  
  
Something like that  
```cpp  
template <class HandshakeF, class CompletionF>  
    WebSocketNotificationsReceiver(const std::string &host, const std::string &method, HandshakeF handshakeDecorator, CompletionF completionHandler) : mClosed(false),  
        mThread(ThreadHelper::StartInterruptableThread([this, host, method](HandshakeF handshake, CompletionF completion)  
        {  
            boost::asio::io_service ios;  
            boost::asio::ip::tcp::resolver r{ios};  
            boost::asio::ip::tcp::socket sock{ios};  
            boost::asio::connect(sock, r.resolve(boost::asio::ip::tcp::resolver::query{host, "8089"}));  
              
            this->mWs = boost::make_shared<beast::websocket::stream<boost::asio::ip::tcp::socket&>>(sock);  
            this->mWs->handshake(host, method);  
            while (true)  
            {  
                boost::this_thread::interruption_point();  
  
                beast::streambuf sb;  
                beast::websocket::opcode op;  
                boost::system::error_code error;  
                this->mWs->read(op, sb, error);  
                if (!error && op == beast::websocket::opcode::text)  
                {  
                    //some code  
                }  
            }  
        }, handshakeDecorator, completionHandler)  
    )  
    {  
    }  
```

---

## Comment 3

> Username: mika-fischer  
> Created at: 2017-04-18 11:31:58 UTC  
> Url: https://github.com/boostorg/beast/issues/306#issuecomment-294795936  

The main disadvantages of a threaded approach are:  
- it doesn't scale well (threads need resources)  
- it has overhead (spawning new threads takes time), and  
- it's less parallel (with async i.o. you can wait for a read to complete, wait for a write to complete and do some work, all at the same time, while with sync IO you can only do one of those things at a time)  
  
That said, it is significantly simpler to program this way if you're not experienced with async IO and for some (many?) problems, the disadvantages mentioned above are not relevant.  
  
You will have to decide, given the problem at hand...

---

## Comment 4

> Username: markvasiv  
> Created at: 2017-04-18 11:49:31 UTC  
> Url: https://github.com/boostorg/beast/issues/306#issuecomment-294803240  

Ok, thanks again! I will probably close this issue, as soon as everything is clear now
