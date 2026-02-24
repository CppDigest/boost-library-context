# #1539 - Memory leak when using control_callback [Closed]

> Username: bandzaw  
> Created at: 2019-03-22 12:15:04 UTC  
> Updated at: 2019-04-19 01:32:42 UTC  
> Closed at: 2019-04-19 01:32:42 UTC  
> Url: https://github.com/boostorg/beast/issues/1539  

Hello,    
I noticed that my client side system leaks memory. I suspect it is somehow due to the control_callback handler is prohibiting the session to be properly cleaned up.    
I'm on Ubuntu 18.04, gcc 7.3.0, Boost.Beast.Version 189 (from usr/local/include/boost/beast/version.hpp)  
  
I can reproduce this by taking the vanilla websocket_client_async_ssl example and adding the following:  
```         
ws_.control_callback(  
    std::bind(  
        &session::onControlMessage,  
        shared_from_this(),  
        std::placeholders::_1,  
        std::placeholders::_2  
    )  
);  
```  
Without the control_callback set I can see the printout of session::dtor but when it is set I cannot see it, indicating the session destructor is never called.    
Additionally, if a add a call to: `ws_.control_callback();` to reset the control_callback in the on_read as follows:  
```  
    void  
    on_read(  
        boost::system::error_code ec,  
        std::size_t bytes_transferred)  
    {  
        boost::ignore_unused(bytes_transferred);  
  
        if(ec)  
        {  
            ws_.control_callback();  
            return fail(ec, "read");  
        }  
```  
the session's destructor gets called.  
  
Hence my suspicious mind that there is a memory leak caused by Boost.Beast somewhere. But I may be wrong, as is quite normal. I would very much appreciate to get an expert opinion on this though!  
  
Thanks in advance,  
Tommy

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-03-22 12:17:03 UTC  
> Url: https://github.com/boostorg/beast/issues/1539#issuecomment-475599304  

Use this instead:  
```  
ws_.control_callback(  
    std::bind(  
        &session::onControlMessage,  
        this, // don't use shared_from_this() here, otherwise it creates a cycle!  
        std::placeholders::_1,  
        std::placeholders::_2  
    )  
);  
```

---

## Comment 2

> Username: bandzaw  
> Created at: 2019-03-22 12:20:46 UTC  
> Url: https://github.com/boostorg/beast/issues/1539#issuecomment-475600360  

Thx for your quick response Vinnie!  
Ok, I can see your point but then I'm thinking when should I use this and when should I use shared_from_this? Everywhere else is shared from this() used...

---

## Comment 3

> Username: vinniefalco  
> Created at: 2019-03-22 12:32:55 UTC  
> Url: https://github.com/boostorg/beast/issues/1539#issuecomment-475604066  

Use `shared_from_this` when you want to extend the lifetime of the socket.

---

## Comment 4

> Username: bandzaw  
> Created at: 2019-03-22 12:46:30 UTC  
> Url: https://github.com/boostorg/beast/issues/1539#issuecomment-475608075  

Ok, I guess the documentation of control_frame could be improved and say something about this even though this is a general issue. I guess the same is true for timers and anything else that are scheduled on the io_context but should not extend the socket's lifetime when the connection goes down? Or are there exceptions to this rule?

---

## Comment 5

> Username: jhmmufc  
> Created at: 2019-03-23 11:08:01 UTC  
> Url: https://github.com/boostorg/beast/issues/1539#issuecomment-475860741  

Generally the timers in the example is used as part of a "session", not directly correlated to the socket per se.   
  
The rule of thumb I use, and the examples use, is that anything that you want to extend the lifetime of the session should use shared_from_this i.e. a completed read, a completed write, a timeout check. All of the above can be cancelled either explicitly i.e. timer.cancel or by stopping the io context. We handle any errors and no longer extend the lifetime of the session object.  
  
Control callback is more of an observable function called internally whenever a ping, pong, close frame is recieved. Therefore binding it to shared_from_this means each session will live indefinitely even with no activity on the socket. Its the same reason why in the control callback you should not call something like socket::close because otherwise the socket cannot correctly process the incoming control frame.  
  
You can bind the weak_from_this or this. weak_from_this is my preference just to make it more explicit that this function will only ever be called when something else is owning of the session.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2019-03-23 12:40:55 UTC  
> Updated at: 2019-03-23 12:41:01 UTC  
> Url: https://github.com/boostorg/beast/issues/1539#issuecomment-475866532  

Another way to look at it is like this:  
* `unique_ptr`: Unique ownership. One owner.  
* `shared_ptr`: Shared ownership. Multiple owners.  
  
By storing `session::shared_from_this` in the control callback, you indicate that the websocket stream shares ownership of the session. But the session already owns the websocket stream (as a data member). This creates a cycle or loop, which is the canonical memory leak to be avoided when using shared_ptr:  
https://stackoverflow.com/questions/776042/how-to-detect-cycles-when-using-shared-ptr

---

## Comment 7

> Username: vinniefalco  
> Created at: 2019-04-19 01:32:42 UTC  
> Url: https://github.com/boostorg/beast/issues/1539#issuecomment-484740194  

It looks like this issue is resolved so I'll go ahead and close it. Feel free to comment on it if you need more information, or create a new issue as needed - thanks!
