# #2409 - [question][websocket] Crashed while trying to reconnect to a websocket connection : ( [Closed]

> Username: ILikeIneine  
> Created at: 2022-04-20 08:10:40 UTC  
> Updated at: 2022-04-20 14:03:51 UTC  
> Closed at: 2022-04-20 13:57:36 UTC  
> Url: https://github.com/boostorg/beast/issues/2409  

### Version of Beast  
  
BOOST_BEAST_VERSION 322  
  
### Steps necessary to reproduce the problem  
  
here is my issue codes: [demo codes & some pics](https://github.com/ILikeIneine/boost-websocket-issue)  
  
I'm trying to create a websocket client and need sending message to server, for example, every 30 second, just like a heartbeat.   
So I wrote the demo above.  
  
I'm new to boost beast and I've meet some problems when trying to implement the reconnection feature.  
  
In client, the flow of my codes is:  
1. `async_resolve` endpoints  
2. `async connect`, if faild close websocketstream and reconnect after a while;  
3. `async_handshake`  
4. start `async_read` and `async_write` like  
```   
AsyncRead()  
{  
    buffer_.consume(buffer_.size());  
    ws_.async_read(buffer_,  
        [self{ this->shared_from_this() }](beast::error_code ec, std::size_t byteTransferred)->void  
    {  
        if (ec)  
        {  
             TryReconnect();    // goes to step 2   
        }  
        self->AsyncRead();  
    });  
}  
```  
  
And what bother me a lot was when I emulated the situation as server disconnect:  
1. Start server and client  
2. I shutdown my server program **for the first time**  
3. `async_read` 's error_code was `system 10054` & `async_write`'s error_code was `system 995`.  
4. The client was still running ok, and started trying to connect remote server.  
5. I restarted my server program **for the second time**  
6. This time `async_read` 's error_code  & `async_write`'s error_code was both `system 995`. And the client crashed instantly with a vc runtime abort()  
  
I've tried lots of times, and get the same result. I wonder what had happened ..  
It's weird that it didn't crash at the first time a close the server, and always accured after the second time.   
  
I desperately want to know the cause of the problem and could you tell me what's the correct way to do the reconnect to websocket or how to handle this situation(reconnect to remote server) gracefully?  
  
### All relevant compiler information  
  
no compiler error, runtime crashed..  
  
### Others  
  
I'd really really really really appreciate it, for this has tortured me very very long :(

---

## Comment 1

> Username: madmongo1  
> Created at: 2022-04-20 09:21:16 UTC  
> Url: https://github.com/boostorg/beast/issues/2409#issuecomment-1103685782  

You cannot reconnect a beast::websocket::stream - you need to destroy it and recreate it before attempting reconnection.

---

## Comment 2

> Username: ILikeIneine  
> Created at: 2022-04-20 09:39:11 UTC  
> Url: https://github.com/boostorg/beast/issues/2409#issuecomment-1103710830  

um... so calling `websocket::stream::close()` while disconnected was wrong and what I need to do is recreate websocket::stream with `net::make_strand` again to restart a connect quest, am I right?

---

## Comment 3

> Username: madmongo1  
> Created at: 2022-04-20 09:40:55 UTC  
> Url: https://github.com/boostorg/beast/issues/2409#issuecomment-1103713212  

You probably don't need to make a new strand, but you will need a newly constructed stream.

---

## Comment 4

> Username: ILikeIneine  
> Created at: 2022-04-20 09:55:52 UTC  
> Url: https://github.com/boostorg/beast/issues/2409#issuecomment-1103734265  

Sorry I'm not quite sure what's the operations to do this. I stored the websocket stream as a class member, and construct it using strand. Is it possible to make this websocket stream recreate again as the class member?

---

## Comment 5

> Username: ILikeIneine  
> Created at: 2022-04-20 10:03:08 UTC  
> Updated at: 2022-04-20 10:05:39 UTC  
> Url: https://github.com/boostorg/beast/issues/2409#issuecomment-1103744759  

em.. what I mean exactly is could I do something to recreate it here:  
```  
void HeartBeatSession::TryReconnect()  
{  
    if (ws_.is_open())  
    {  
        ws_.close(websocket::close_code::normal);  
    }  
  
    //  is it possible to do something here to reconstruct ws_ ?  
  
    const auto this_step = CurrentSpan();  
    std::cout << "next trial will start after :" << this_step << "ms \n";  
    std::this_thread::sleep_for(std::chrono::milliseconds(this_step));  
    AsyncConnect();  
}  
```

---

## Comment 6

> Username: sehe  
> Created at: 2022-04-20 12:32:48 UTC  
> Url: https://github.com/boostorg/beast/issues/2409#issuecomment-1103878832  

So, made it self-contained: https://github.com/sehe/boost-websocket-issue/tree/sehe  
  
Now I can make it fail to reproduce your problem:  
  
https://user-images.githubusercontent.com/324097/164229792-34430179-ff6e-44de-8609-d395e13fca90.mp4  
  
 Now I'd use e.g. unique_ptr or optional to make ws_ dynamically/lazy initializable. In this case we only need the resettability:  
  
```c++  
    std::optional<websocket::stream<beast::tcp_stream>> ws_;  
  
//...  
    ws_.emplace(ws_->get_executor());  
```  
  
See it working:  
  
https://user-images.githubusercontent.com/324097/164231032-2122bb13-8548-4fbc-b385-75a57ec71b3a.mp4

---

## Comment 7

> Username: sehe  
> Created at: 2022-04-20 13:45:12 UTC  
> Url: https://github.com/boostorg/beast/issues/2409#issuecomment-1103951473  

I'll close this issue. Feel free to re-open/post follow-up if you have remaining questions @ILikeIneine

---

## Comment 8

> Username: ILikeIneine  
> Created at: 2022-04-20 14:03:51 UTC  
> Url: https://github.com/boostorg/beast/issues/2409#issuecomment-1103972873  

This really helps me a lot as a beginner. Thanks very much!
