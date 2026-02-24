# #1712 - boost/beast: idle timeout of websocket can be delivered to caller by async_write? [Open]

> Username: asdfping  
> Created at: 2019-09-25 16:18:52 UTC  
> Updated at: 2019-09-26 01:46:56 UTC  
> Url: https://github.com/boostorg/beast/issues/1712  

**Environment:**  
MacOS 10.14.6  
boost version: 1.71  
  
**Desc：**  
boost/beast: idle timeout of websocket can be delivered to caller by invoking the completion handler for `async_write`?  
  
I am testing the timeout mechanism of beast websocket.I found that **idle timeout is received by handler for `async_write`**。I'm not sure if this is ok. The beast document only say we can use `async_read` to detect idle timout.  
  
**Detail：**  
I'm using boost/beast 1.71 to develop websocket client program. According to the [document](https://www.boost.org/doc/libs/1_71_0/libs/beast/doc/html/beast/using_websocket/timeouts.html), in order to enable idle_timeout of websocket, I should set the websocket parameter as follows:  
```c++  
stream_base::timeout opt{  
    std::chrono::seconds(20),   // handshake timeout  
    std::chrono::seconds(6),       // idle timeout. no data recv from peer for 6 sec, then it is timeout  
    true   //enable ping-pong to keep alive  
};  
  
ws.set_option(opt);  
```  
I made the above settings, then call `async_read` to recv idle_timeout notification. like this:  
async_read code:  
```c++  
ws_.async_read(buffer_, beast::bind_front_handler(&WebsocketSession::on_read, shared_from_this()));  
  
void WebsocketSession::on_read(beast::error_code ec, std::size_t bytes_transferred)  
{  
    if (ec)  
    {  
        fail(ec, "read");  
        if (ec == beast::error::timeout)  
        {  
            std::cout << show_current_time() << "|idle timeout when read" << std::endl; //idle_timeout   
        }  
    }  
  
    buffer_.clear();  
    ws_.async_read(buffer_, beast::bind_front_handler(&WebsocketSession::on_read, shared_from_this()));  
}  
```  
My websock client program need to send multiple messages at the same time. So according to  #1207  , I use queue to support multiple messages sending.  
  
To verify the reliability of sending multiple messages simultaneously, test client program will   
send messages to echo server by websocket constantly. At the same time, to verify idle timeout, the echo server will sleep after recv some messages so the test client can't recv any message for some seconds, that can cause idle_timeout.  
  
according to document, idle timeout should be recv by completion handler for `async_read`. But in my test, **completion handler for`async_write` can also recv idle timeout**. Like this:  
  
async_write code:  
```c++  
ws_.async_write(net::buffer(msg), beast::bind_front_handler(&WebsocketSession::on_write, shared_from_this()));  
  
void WebsocketSession::on_write(beast::error_code ec, std::size_t bytes_transferred)  
{  
    boost::ignore_unused(bytes_transferred);  
    // use queue to send message  
    sendMessageQueue_.pop_front();  
    if (ec)  
    {  
        fail(ec, "write");  
        if(ec == beast::error::timeout)  
        {  
            std::cout << show_current_time() << "| idle timeout when write" << std::endl; //async_write recv timeout  
        }  
    }  
    if (!sendMessageQueue_.empty())  
    {  
        auto &frontMsg = sendMessageQueue_.front();  
        doSend(frontMsg);  
    }  
}  
```  
my test log:  
```  
run start  
2019-09-25 22:24:38|recv:userMsg:1 |total recv:1  
2019-09-25 22:24:38|recv:userMsg:2 |total recv:2  
2019-09-25 22:24:38|recv:userMsg:3 |total recv:3  
2019-09-25 22:24:38|recv:userMsg:4 |total recv:4  
2019-09-25 22:24:38|recv:userMsg:5 |total recv:5  
2019-09-25 22:24:38|recv:userMsg:6 |total recv:6  
2019-09-25 22:24:38|recv:userMsg:7 |total recv:7  
2019-09-25 22:24:38|recv:userMsg:8 |total recv:8  
2019-09-25 22:24:38|recv:userMsg:9 |total recv:9  
2019-09-25 22:24:38|recv:userMsg:10 |total recv:10  
2019-09-25 22:24:38|recv:userMsg:11 |total recv:11  
2019-09-25 22:24:44|write: The socket was closed due to a timeout  //<--after 6 seconds, async_write recv idle timeout.  
2019-09-25 22:24:44| idle timeout when write  
2019-09-25 22:24:44|read: Operation canceled  
```  
  
I guess the reason is that there are both `async_read` and `async_write` , when the timeout is triggered, the handler for `async_write` recv timeout firstly.   
  
So my problem is: Can **idle timeout of websocket** be also delivered to caller by invoking completion handler for **`async_write`** ? The document only say `async_read` can recv idle timeout, so I'm not sure if I can detect idle timeout with handle for `async_write`.  
  
Another: except `async_read` and `async_write`，does any other `async_xxx`(such as `async_ping`,`async_pong`) can alos recv idle_timeout?  
  
This is [my test program code](https://github.com/asdfping/beast_test)  
  
Thank You!

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-09-25 16:36:54 UTC  
> Url: https://github.com/boostorg/beast/issues/1712#issuecomment-535106686  

> does any other async_xxx(such as async_ping,async_pong) can alos recv idle_timeout?  
  
Yes actually, looking at the implementation *any* asynchronous websocket operation can receive the error `websocket::error::timeout`. The docs need to be updated.

---

## Comment 2

> Username: asdfping  
> Created at: 2019-09-26 01:46:39 UTC  
> Url: https://github.com/boostorg/beast/issues/1712#issuecomment-535293718  

Ok,Thank You. @vinniefalco
