# #1774 - websocket async_write message buffer lifetime control problem [Closed]

> Username: bbbbyang  
> Created at: 2019-11-30 05:22:15 UTC  
> Updated at: 2020-01-03 17:00:27 UTC  
> Closed at: 2020-01-03 17:00:27 UTC  
> Url: https://github.com/boostorg/beast/issues/1774  

https://github.com/boostorg/beast/issues/1707  
This was my first time when I met this issue. Vinnie helped me notice the message buffer lifetime problem. I followed the suggestion.  
  
```  
sendMessage( const std::string& serializedMessage )  
{  
    _mtx.lock();  
    if( this->_isConnected )  
    {  
        this->_messageQueue.push( serializedMessage );  
        if( !this->_isWrite )  
        {  
            this->_isWrite = true;  
            _webSocket.async_write(  
                    boost::asio::buffer( this->_messageQueue.front() ),  
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
```  
onWrite( ... )  
{  
    _messageQueue.pop();  
    _isWrite = false;  
    ....  
    if( !_messageQueue.isEmpty() )  
    {  
        string message = _messageQueue.front();  
        _messageQueue.pop();  
        sendMessage(message);  
    }  
}  
```  
So in theory, I have a messageQueue, which is not local queue, to control the life time of my message. The message should be sent completely. (I know there is a logic problem of the sequence of message that is okay for now and I will change it to a vector and follow beast chat-multi example)  
  
Occasionally, I still get the complain about message can not parsed by protobuf losing some bytes at the end same in #1707 and some time even with a segmentation fault complained about __memcpy_. And in #1707 you mention to use `vector<shared_ptr<string>>`.  #1140, beast chat-multi, and some stackoverflow issues, they all use this `shared_ptr<string>` or `ref(string)`instead of `std::string` for asio buffer. I have read the link you left in my last issue about small string optimization. But I dont fully understand what it may affect my code

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-11-30 14:28:20 UTC  
> Url: https://github.com/boostorg/beast/issues/1774#issuecomment-559974798  

As I said in #1707, you can't use `vector<string>`. Use one of the alternatives I listed.

---

## Comment 2

> Username: bbbbyang  
> Created at: 2019-11-30 15:39:28 UTC  
> Updated at: 2019-11-30 16:13:27 UTC  
> Url: https://github.com/boostorg/beast/issues/1774#issuecomment-559984665  

`vector<string>` has nothing with the lifetime problem?  
All my messages are quite big, I think they should be allocated in heap.  
when string combined withe vector, that is where sso will have a problem?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2019-11-30 16:50:05 UTC  
> Url: https://github.com/boostorg/beast/issues/1774#issuecomment-559994477  

If your strings are all large, you are still doing something wrong. Perhaps a missing strand, or you are accessing the stream in ways that are not thread-safe (such as concurrent access). I can't see your entire program (nor would I want to). Have you tried running under valgrind or another sanitizer?

---

## Comment 4

> Username: stale[bot]  
> Created at: 2019-12-30 17:05:56 UTC  
> Url: https://github.com/boostorg/beast/issues/1774#issuecomment-569735806  

This issue has been open for a while with no activity, has it been resolved?
