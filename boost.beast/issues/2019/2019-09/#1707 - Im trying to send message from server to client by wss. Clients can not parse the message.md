# #1707 - Im trying to send message from server to client by wss. Clients can not parse the message [Closed]

> Username: bbbbyang  
> Created at: 2019-09-18 21:09:08 UTC  
> Updated at: 2019-09-19 00:15:03 UTC  
> Closed at: 2019-09-19 00:15:03 UTC  
> Url: https://github.com/boostorg/beast/issues/1707  

before I use boost tls, it works fine. Then I add TLS on websocket followed by the 1_66_0 example.  
Server keeps sending same messages to client. One or two messages can not get parsed by protobuf, others can get prased. All of the mssages are same   
  
will TLS mess up with the bytes ?  
  
this is how the server sends message  
```  
sendMessage( const std::string& serializedMessage )  
{  
    _mtx.lock();  
    if( this->_isConnected )  
    {  
        if( this->_isWriting )  
        {  
            this->_messageQueue.push( serializedMessage );  
        }  
        else  
        {  
            this->_isWriting = true;  
            _webSocket.async_write(  
                    boost::asio::buffer( serializedMessage ),  
                    std::bind(  
                            &Session<MsgBaseRcv, MsgUtilsRcv, MsgBaseSend, MsgUtilsSend>::onWrite,  
                            this->shared_from_this(),  
                            std::placeholders::_1,  
                            std::placeholders::_2 ) );  
        }  
    }  
    _mtx.unlock();  
}  
```  
  
this is how client receive message  
```  
onRead( boost::system::error_code error, std::size_t bytesTransferred )  
{  
    if( _isConnected )  
    {  
        boost::ignore_unused( bytesTransferred );  
        if( error )  
        {  
            onClose( error );  
            fail( error, "read" );  
        }  
        else  
        {  
            doRead();  
  
            std::ostringstream os;  
            os << boost::beast::buffers( _buffer.data() );  
            std::string serializedMessage = os.str();  
            _buffer.consume( _buffer.size() );  
            deserializeMessageAndPassToSessionHandler( serializedMessage );  
        }  
    }  
}  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-09-18 21:50:41 UTC  
> Url: https://github.com/boostorg/beast/issues/1707#issuecomment-532881151  

Are you setting the message type to **binary** ?

---

## Comment 2

> Username: bbbbyang  
> Created at: 2019-09-18 21:59:17 UTC  
> Updated at: 2019-09-18 22:00:53 UTC  
> Url: https://github.com/boostorg/beast/issues/1707#issuecomment-532883691  

yes. in run function  
```  
run()  
{  
    _webSocket.binary( true );  
...........  
}  
```  
I checked the size of the message client received. Size is right, but content messed up at the end of the message

---

## Comment 3

> Username: vinniefalco  
> Created at: 2019-09-18 23:12:42 UTC  
> Updated at: 2019-09-18 23:12:55 UTC  
> Url: https://github.com/boostorg/beast/issues/1707#issuecomment-532901523  

And how are you making sure that the lifetime of the `serializedMessage` string parameter extends until the `async_write` operation is complete?

---

## Comment 4

> Username: bbbbyang  
> Created at: 2019-09-18 23:17:24 UTC  
> Updated at: 2019-09-18 23:25:35 UTC  
> Url: https://github.com/boostorg/beast/issues/1707#issuecomment-532902573  

I checked the size of serializedMessage received. the size is right  
so how can I know when the message sent completely? is there any callback can let me know in boost library?

---

## Comment 5

> Username: vinniefalco  
> Created at: 2019-09-18 23:27:14 UTC  
> Url: https://github.com/boostorg/beast/issues/1707#issuecomment-532904609  

Yes the size will be right because `websocket::async_write` looks at the string before the initiating function returns. But you are not doing anything to ensure that the lifetime of the parameter extends until the completion handler is finished. So your string is being destroyed before the operation finishes. You could just always put the string in the queue:  
```  
sendMessage( const std::string& serializedMessage )  
{  
    _mtx.lock();  
    if( this->_isConnected )  
    {  
        this->_messageQueue.push( serializedMessage );  
        if( this->_messageQueue.size() == 1) // first time  
            _webSocket.async_write(  
                    boost::asio::buffer( this->_messageQueue.front() ),  
                    std::bind(  
                            &Session<MsgBaseRcv, MsgUtilsRcv, MsgBaseSend, MsgUtilsSend>::onWrite,  
                            this->shared_from_this(),  
                            std::placeholders::_1,  
                            std::placeholders::_2 ) );  
    }  
    _mtx.unlock();  
}  
```  
  
However, you have another little problem. If your standard library uses the "small string optimization" (and most of them do) then the buffer for the string might be inside the message rather than in dynamically allocated memory. See:  
  
https://shaharmike.com/cpp/std-string/  
  
You have some choices here. Instead of using `vector<string>` you could use `vector<pair<size_t, unique_ptr<char[]>>>`. Or you could use `vector<shared_ptr<string>>`. There's a Beast example which does something similar to your code, perhaps you could copy it:  
  
https://github.com/boostorg/beast/blob/55fd67b8740d79ec63e6c17107d447db7c9a3082/example/websocket/server/chat-multi/websocket_session.cpp#L103

---

## Comment 6

> Username: bbbbyang  
> Created at: 2019-09-19 00:15:03 UTC  
> Url: https://github.com/boostorg/beast/issues/1707#issuecomment-532914025  

Nice!!! thanks for your help!!!!
