# #1127 - how to close websocket  in server side?? got close_wait [Closed]

> Username: ghost  
> Created at: 2018-05-15 12:09:56 UTC  
> Updated at: 2022-05-10 15:55:02 UTC  
> Closed at: 2018-05-18 09:39:12 UTC  
> Url: https://github.com/boostorg/beast/issues/1127  

here is some main code   
```  
void websocket_session::on_read(boost::system::error_code ec,std::size_t bytes_transferred)  
{  
    if (ec) {  
  //1 run here  
        LogError("websocket_session::on_read error!! %s\r\n", boost::system::system_error(ec).what());  
        return do_close();  
    }  
}  
  
void websocket_session::do_close()  
{  
    // 2 Close the WebSocket Connection  
 timer_.cancel();  
    ws_.async_close(  
        websocket::close_code::normal,  
        boost::asio::bind_executor(  
            strand_,  
            std::bind(  
                &websocket_session::on_close,  
                shared_from_this(),  
                std::placeholders::_1)));  
  
}  
void websocket_session::on_close(boost::system::error_code ec)  
{  
    if (ec) {  
   // 3  
        LogError("websocket_session::on_close error!! %s\r\n", boost::system::system_error(ec).what());  
    }  
      
}  
  
  
```  
  
websocket_session::on_read error!! The WebSocket stream was gracefully closed at both endpoints  
websocket_session::on_close error!! Operation canceled  
  
  
tcp        0      0 0.0.0.0:80              0.0.0.0:*               LISTEN      24582/./test[0] 0:root@bigdata3:/home/beast-boost-1.67.0/etcp        0      0 127.0.0.1:80            127.0.0.1:45975         TIME_WAIT   -                                              tcp        0      0 127.0.0.1:80            127.0.0.1:45974         CLOSE_WAIT  24582/./test

---

## Comment 1

> Username: ghost  
> Created at: 2018-05-15 12:13:21 UTC  
> Url: https://github.com/boostorg/beast/issues/1127#issuecomment-389144665  

tcp        0      0 127.0.0.1:80            127.0.0.1:45974         CLOSE_WAIT  24582/./test  
websocket_session do not call destructor !!  
  
below also in my websocket_session   
```  
ws_.control_callback(std::bind(&websocket_session::on_control_callback,  
        shared_from_this(),std::placeholders::_1,std::placeholders::_2));  
```

---

## Comment 2

> Username: vinniefalco  
> Created at: 2018-05-15 14:09:54 UTC  
> Updated at: 2018-05-15 14:11:21 UTC  
> Url: https://github.com/boostorg/beast/issues/1127#issuecomment-389180800  

>ws_.control_callback(std::bind(&websocket_session::on_control_callback, shared_from_this(),std::placeholders::_1,std::placeholders::_2));  
  
The bind wrapper is maintaining reference to the shared pointer, creating a cycle. The control callback is not a completion handler, you can use `this` instead of a shared pointer:  
  
```  
ws_.control_callback(  
    std::bind(  
        &websocket_session::on_control_callback,  
        this,  
        std::placeholders::_1,  
        std::placeholders::_2));  
```  
  
The advanced examples demonstrate correct usage of the control callback:  
https://github.com/boostorg/beast/blob/344d957f4260bff5ef9c511e18e5060c5ace2fa4/example/advanced/server/advanced_server.cpp#L247

---

## Comment 3

> Username: ghost  
> Created at: 2018-05-15 14:28:24 UTC  
> Url: https://github.com/boostorg/beast/issues/1127#issuecomment-389187364  

when use this pointer ，after websocket_session destruct，if  
on_control_callback called，will access object memory that has been  
released？I'm curious why stream is closed but on_control_callback didn't  
called？ usually read hanlder and write hanlder will called..  
Vinnie Falco <notifications@github.com>于2018年5月15日 周二22:10写道：  
  
> ws_.control_callback(std::bind(&websocket_session::on_control_callback,  
> shared_from_this(),std::placeholders::_1,std::placeholders::_2));  
>  
> The bind wrapper is maintaining reference to the shared pointer, creating  
> a cycle. The control callback is not a completion handler, you can use  
> this instead of a shared pointer:  
>  
> ws_.control_callback(  
>     std::bind(  
>         &websocket_session::on_control_callback,  
>         this,  
>         std::placeholders::_1,  
>         std::placeholders::_2));  
>  
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/beast/issues/1127#issuecomment-389180800>,  
> or mute the thread  
> <https://github.com/notifications/unsubscribe-auth/AI4XbRSSnySpnJqvRyqsH6xqB1rhrX0vks5tyuG2gaJpZM4T_bJu>  
> .  
>

---

## Comment 4

> Username: ghost  
> Created at: 2018-05-15 14:29:40 UTC  
> Url: https://github.com/boostorg/beast/issues/1127#issuecomment-389187824  

Yang Liu <ly901206@gmail.com>于2018年5月15日 周二22:27写道：  
  
> when use this pointer ，after websocket_session destruct，if  
> on_control_callback called，will access object memory that has been  
> released？I'm curious why stream is closed but on_control_callback didn't  
> called？ usually read hanlder and write hanlder will called..  
> Vinnie Falco <notifications@github.com>于2018年5月15日 周二22:10写道：  
>  
>> ws_.control_callback(std::bind(&websocket_session::on_control_callback,  
>> shared_from_this(),std::placeholders::_1,std::placeholders::_2));  
>>  
>> The bind wrapper is maintaining reference to the shared pointer, creating  
>> a cycle. The control callback is not a completion handler, you can use  
>> this instead of a shared pointer:  
>>  
>> ws_.control_callback(  
>>     std::bind(  
>>         &websocket_session::on_control_callback,  
>>         this,  
>>         std::placeholders::_1,  
>>         std::placeholders::_2));  
>>  
>> —  
>> You are receiving this because you authored the thread.  
>> Reply to this email directly, view it on GitHub  
>> <https://github.com/boostorg/beast/issues/1127#issuecomment-389180800>,  
>> or mute the thread  
>> <https://github.com/notifications/unsubscribe-auth/AI4XbRSSnySpnJqvRyqsH6xqB1rhrX0vks5tyuG2gaJpZM4T_bJu>  
>> .  
>>  
>

---

## Comment 5

> Username: vinniefalco  
> Created at: 2018-05-15 15:03:35 UTC  
> Url: https://github.com/boostorg/beast/issues/1127#issuecomment-389200352  

> when use this pointer ，after websocket_session destruct，if on_control_callback called，will access object memory that has been released  
  
No.  
  
> I'm curious why stream is closed but on_control_callback didn't called？ usually read hanlder and write hanlder will called..  
  
The control callback is not a completion handler. It is only invoked from read operations. If you do not have any read operation active, then the control handler will not be called.

---

## Comment 6

> Username: ghost  
> Created at: 2018-05-15 15:06:14 UTC  
> Url: https://github.com/boostorg/beast/issues/1127#issuecomment-389201354  

i undetstand. thx！  
  
Vinnie Falco <notifications@github.com>于2018年5月15日 周二23:03写道：  
  
> when use this pointer ，after websocket_session destruct，if  
> on_control_callback called，will access object memory that has been released  
>  
> No.  
>  
> I'm curious why stream is closed but on_control_callback didn't called？  
> usually read hanlder and write hanlder will called..  
>  
> The control callback is not a completion handler. It is only invoked from  
> read operations. If you do not have any read operation active, then the  
> control handler will not be called.  
>  
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/beast/issues/1127#issuecomment-389200352>,  
> or mute the thread  
> <https://github.com/notifications/unsubscribe-auth/AI4XbVWYv_Q4EPmE7Y1kEUSaR9kqxS3rks5tyu5MgaJpZM4T_bJu>  
> .  
>

---

## Comment 7

> Username: RavikumarTulugu  
> Created at: 2022-05-10 15:55:02 UTC  
> Url: https://github.com/boostorg/beast/issues/1127#issuecomment-1122583476  

i am facing the exact same issue, seeing lot of connections in close_wait state. i have a solution in mind, wanted to check before implementing it. what if we send a post event to the io_context from the read hander on detecting the connection as closed and in the post handler we close the websocket. are there any problems with this approach ??
