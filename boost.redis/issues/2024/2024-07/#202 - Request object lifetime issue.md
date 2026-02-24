# #202 - [Question] Request object lifetime issue [Closed]

> Username: jarni-ua  
> Created at: 2024-07-03 13:13:54 UTC  
> Updated at: 2024-07-17 09:40:03 UTC  
> Closed at: 2024-07-17 09:39:50 UTC  
> Url: https://github.com/boostorg/redis/issues/202  

Hi, I'm having trouble understanding the boost::redis::request object lifetime requirements.   
Here is my app setup  
1. It is asio based web server  
2. Redis connection doesn't use co-routines but a normal io_executor used to handle my web server requests  
```   
    redis_ = std::make_shared<boost::redis::connection>(ios_);  
    boost::redis::config cfg;  
    cfg.addr = boost::redis::address{appConfig.redis.host.value_or("localhost"), std::to_string(appConfig.redis.port.value_or(6379))};  
    redis_->async_run(cfg, {}, net::consign(net::detached, redis_));  
```  
3. On startup, I create a connection and subscribe to channel of interest (main thread)  
```  
    boost::redis::request req;  
    req.push("SUBSCRIBE", "mychannel");  
    redis_->async_exec(req, boost::redis::ignore, net::detached);  
```  
4. On startup I start async_receive "loop" to retreive notifications from the channel I subscribed. The redisResponse_ is a member of the app class and exceeds the lifetime of redis connection. (main thread)  
```  
    redis_->set_receive_response(redisResponse_);  
    redis_->async_receive(std::bind(&MyApp::redisHandler, this, std::placeholders::_1, std::placeholders::_2));  
```  
5. On specific web api call I want to publish my data to the channel (asio worker thread)  
```  
    boost::redis::request req;  
    req.push("PUBLISH", "mychannel", "my data");  
    redis_->async_exec(req, boost::redis::ignore, net::detached);  
```  
6. The handler looks like this (asio worker thread)  
```  
void MyApp::redisHandler(boost::system::error_code ec, std::size_t n) {  
    if (ec) {  
        if (redis_->will_reconnect()) {  
            boost::redis::request req;  
            req.push("SUBSCRIBE", "mychannel");  
            redis_->async_exec(req, boost::redis::ignore, net::detached);  
        }  
    } else {  
        // Here goes processing of data  
        boost::redis::consume_one(redisResponse_);  
        // Ready to receive next one, if any  
        redis_->async_receive(std::bind(&MyApp::redisHandler, this, std::placeholders::_1, std::placeholders::_2));  
    }  
}  
```  
  
As described above, the requests to redis are pushed from different places/threads and the `boost::redis::request req;` is a temporary on stack. But at https://github.com/boostorg/redis/blob/develop/include/boost/redis/detail/connection_base.hpp#L627 the code takes the address of that temporary. It crashes later on in the guts of sending the request, namely here https://github.com/boostorg/redis/blob/develop/include/boost/redis/detail/connection_base.hpp#L783 as the memory pointed by ri->req_ is long gone.  
  
Am I misunderstanding how the library should be used?

---

## Comment 1

> Username: jarni-ua  
> Created at: 2024-07-03 14:28:26 UTC  
> Url: https://github.com/boostorg/redis/issues/202#issuecomment-2206294015  

I ended up using shared pointer to the request object, and response, if needed  
```  
    auto req = std::make_shared<boost::redis::request>();  
    req->push("SUBSCRIBE", "mychannel");  
    redis_->async_exec(*req, boost::redis::ignore, [req](boost::system::error_code, std::size_t n){  
        // Response ignored, do nothing  
    });  
```  
Seems to be pretty stable as the memory is held up until the handler is executed and discarded, which I guess, is the last time when request object is needed.

---

## Comment 2

> Username: mzimbres  
> Created at: 2024-07-06 21:18:29 UTC  
> Url: https://github.com/boostorg/redis/issues/202#issuecomment-2211972245  

Hi,  
  
> Seems to be pretty stable as the memory is held up until the handler is executed and discarded, which I guess, is the last time when request object is needed.   
  
This is correct, you must guarantee the request lifetime yourself, the library won't care about that for you. Alternatively you could have used the `asio::consign` token instead of a dummy lambda, but it is not required.

---

## Comment 3

> Username: jarni-ua  
> Created at: 2024-07-17 09:39:50 UTC  
> Updated at: 2024-07-17 09:40:03 UTC  
> Url: https://github.com/boostorg/redis/issues/202#issuecomment-2232879772  

@mzimbres Thank you for clarification.  
> you must guarantee  
  
Unfortunately this is not mentioned anywhere. It might be very useful to have it in the parameter's description or somewhere else in documentation.
