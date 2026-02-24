# #1745 - boost::thread_resource_error: Resource temporarily unavailable [Closed]

> Username: bbbbyang  
> Created at: 2019-10-25 16:22:45 UTC  
> Updated at: 2019-10-28 20:17:29 UTC  
> Closed at: 2019-10-28 20:17:29 UTC  
> Url: https://github.com/boostorg/beast/issues/1745  

Currently I have a websocket client and make io_context running in boost::thread  
```  
void websockets::Client<MsgBaseRcv, MsgUtilsRcv, MsgBaseSend, MsgUtilsSend>::  
start()  
{  
    if( !_isStarted )  
    {  
        _isStarted = true;  
  
        DBG_DETAIL( _dbg, "Attempting connection to " << _serverAddress << ":" << _serverPort );  
        _session.reset();  
        _session.reset( new ClientSession<MsgBaseRcv, MsgUtilsRcv, MsgBaseSend, MsgUtilsSend>(  
                _ioc, _sslContext, this, _serverAddress, _serverPort ) );  
        _session->run();  
  
        boost::thread run_thread([&] {  
            _ioc.run();  
        } );  
    }  
}  
```  
  
In the destructor I call _ioc.stop to hope to end this boost:thread  
```  
websockets::Client<MsgBaseRcv, MsgUtilsRcv, MsgBaseSend, MsgUtilsSend>::  
~Client()  
{  
    _ioc.stop();  
}  
```  
  
In my main task, I have a timer. Every 10 seconds this timer will call restart function if the client can not connect with server  
```  
void  restart()  
{  
    _client->stop();  
    _client.reset(new websockets::Client<MsgBaseRcv, MsgUtilsRcv, MsgBaseSend, MsgUtilsSend>(  
            Ip, Port, this ) );  
    setTlsCert();  
    _client->start();  
}  
```  
  
I just made this running all night. After around 6 hour. this happened 3 times in my over night testing. It will throw a boost::thread_resource_error: Resource temporarily unavailable and restarted the task again. I think it is running thread not end properly but im not 100% sure. How can I make sure that the ioc thread terminated when destructor get called

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-10-25 17:17:59 UTC  
> Url: https://github.com/boostorg/beast/issues/1745#issuecomment-546438099  

no idea. try using `std::thread`?

---

## Comment 2

> Username: bbbbyang  
> Created at: 2019-10-25 17:20:12 UTC  
> Updated at: 2019-10-25 17:23:13 UTC  
> Url: https://github.com/boostorg/beast/issues/1745#issuecomment-546438796  

okay

---

## Comment 3

> Username: bbbbyang  
> Created at: 2019-10-25 17:22:47 UTC  
> Updated at: 2019-10-25 17:52:23 UTC  
> Url: https://github.com/boostorg/beast/issues/1745#issuecomment-546439644  

And I noticed that every 10 secs will throw away old object and make a new one is a bad idea. I should call ioc stop, restart, run for this feature, right?  
But whenever i call ioc.run. It needs a new thread?

---

## Comment 4

> Username: vinniefalco  
> Created at: 2019-10-25 20:02:34 UTC  
> Url: https://github.com/boostorg/beast/issues/1745#issuecomment-546493038  

You gotta know how to use asio

---

## Comment 5

> Username: bbbbyang  
> Created at: 2019-10-27 00:14:49 UTC  
> Updated at: 2019-10-27 00:15:59 UTC  
> Url: https://github.com/boostorg/beast/issues/1745#issuecomment-546650422  

I checked the ps thread info, when I called io_context.stop(). The thread not return, still running  
Can you give me more specific hint where should I start to learn about asio

---

## Comment 6

> Username: vinniefalco  
> Created at: 2019-10-27 00:25:30 UTC  
> Url: https://github.com/boostorg/beast/issues/1745#issuecomment-546650926  

I would do an internet search and try to find the stuff that is out there, I don't have any links handy sorry. Maybe look at the asio examples?
