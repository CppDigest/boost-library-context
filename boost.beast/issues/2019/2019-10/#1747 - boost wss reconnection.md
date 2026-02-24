# #1747 - boost wss reconnection [Closed]

> Username: bbbbyang  
> Created at: 2019-10-29 21:03:14 UTC  
> Updated at: 2019-10-29 22:16:34 UTC  
> Closed at: 2019-10-29 22:16:34 UTC  
> Url: https://github.com/boostorg/beast/issues/1747  

I have a questuon about websocket client reconnection to server.  
Client has a shared_ptr pointing to ClientSession  
If a connection not established, client will call ClientSession::disconnect() and run(...) again  
Here is the code  
```  
clientSession->disconnect()  
clientSession->run(ioc, sslContext)  
ioc.run()  
```  
  
```  
void websockets::ClientSession::disconnect()  
{  
    _webSocket->lowest_layer().close();  
}  
  
void websockets::ClientSession::run( boost::asio::io_context& ioc, boost::asio::ssl::context& sslContext )  
{  
    _webSocket.reset( new boost::beast::websocket::stream<boost::asio::ssl::stream<boost::asio::ip::tcp::socket>>( ioc, sslContext ) );  
    _webSocket->binary( true );  
    _resolver = boost::asio::ip::tcp::resolver( _webSocket->next_layer().next_layer().get_io_context() );  
    _resolver.async_resolve(  
            this->_address.c_str(),  
        std::to_string( this->_port ),  
        std::bind(  
            &ClientSession::onResolve,  
            this->shared_from_this(),  
            std::placeholders::_1,  
            std::placeholders::_2 ) );  
}  
```  
ONLY the first time onResolve will get called, I can see the loggers in onResolve function. After that, all other retrys can not trigger this onResolve function. Where did I miss?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-10-29 21:23:27 UTC  
> Url: https://github.com/boostorg/beast/issues/1747#issuecomment-547635620  

No idea, this sounds like an asio question.

---

## Comment 2

> Username: bbbbyang  
> Created at: 2019-10-29 22:16:11 UTC  
> Updated at: 2019-10-29 22:16:31 UTC  
> Url: https://github.com/boostorg/beast/issues/1747#issuecomment-547653598  

I figure out solutions. It is asio question. sorry I asked here.  
The description of asio async handler in the documents is  
```  
Invocation of the handler will be performed in a manner equivalent to using boost::asio::io_context::post().  
```  
So I think it is because ioc returned. Even though I called ioc.run again.(I dont know why not working)  
There are two ways to solve this.  
1. https://stackoverflow.com/questions/49122521/cant-implement-boostasiosslstreamboostasioiptcpsocket-reconnect  
Set a timer inside clientSession, so in io_context it is always has work to do and io_context will keep running  
  
2. set a work to protect ioc keep running and make a new thread for it  
    _work.reset( new boost::asio::io_service::work( _ioc ) );  
    _thread.reset( new std::thread( [ this ](){  
             _ioc.run();  
    } ) );  
  
I will close it
