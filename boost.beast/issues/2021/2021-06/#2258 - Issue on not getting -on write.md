# #2258 - Issue on not getting "on write" [Closed]

> Username: treyweaver  
> Created at: 2021-06-11 19:05:58 UTC  
> Updated at: 2022-09-24 05:15:22 UTC  
> Closed at: 2022-09-24 05:15:22 UTC  
> Url: https://github.com/boostorg/beast/issues/2258  

I am using the latest version of beast 1.74.  
  
I have an issue where I have a mutex when I do a .async_write and I clear this mutex when I get an on_write event.  I use this to make sure I don't do two writes in a row without waiting for the on_write event.  
  
But sometimes I do a write and I do not get a on_write event.  The mutex gets set and everything stops.  I have tried a writingMtx.try_lock_for(Ms(50))  to try the mutex for 50ms then continue anyway but then when I do another write I get a SIGABRT fault because I did two writes in a row without getting an on_write event.   
  
So how do I get around this problem?  Is there some way to tell websockets to ignore the last write?  
  
```  
    bool CXXXWebSocket::writeBuffer(vector_u8 v, bool isBinary) {  
        using Ms = std::chrono::milliseconds;  
        if (!writingMtx.try_lock_for(Ms(50))) {  
                myLog->warn("Write Buffer Mutex was locked for too long.");  
                boost::ignore_unused(0);  
        }  
        boost::asio::mutable_buffers_1 buf = net::buffer(v.data(), v.size());  
        std::cout << "xmit buffer size: " << buf.size() << std::endl;  
        ws_.binary(true);  
        ws_.async_write(  
                        net::buffer(v.data(), v.size()),  
                        beast::bind_front_handler(  
                            &CJpsWebSocket::on_write,  
                            shared_from_this()));  
        return true;  
    }  
```  
  
Here is my onWrite event.  
  
```  
    void CXXXWebSocket::on_write(  
        beast::error_code ec,  
        std::size_t bytes_transferred)  
    {  
        std::cout << "********** on_write..." << std::endl;  
        writingMtx.unlock();  
        onWriteMutex.lock();  
        boost::ignore_unused(bytes_transferred);  
  
        if(ec) {  
            onWriteMutex.unlock();  
            return fail(ec, "write");  
        }  
  
        if (!readIsReady) {  
            readIsReady = true;  
            readReady();  
        }  
        onWriteMutex.unlock();  
    }  
  
```  
I have the timeouts set up as such.  
  
```  
        // Turn off the timeout on the tcp_stream, because  
        // the websocket stream has its own timeout system.  
        beast::get_lowest_layer(ws_).expires_never();  
  
        // Set suggested timeout settings for the websocket  
        ws_.set_option(  
            websocket::stream_base::timeout::suggested(  
                beast::role_type::client));  
  
        // Set a decorator to change the User-Agent of the handshake  
        ws_.set_option(websocket::stream_base::decorator(  
            [](websocket::request_type& req)  
            {  
                req.set(http::field::user_agent,  
                    std::string(BOOST_BEAST_VERSION_STRING) +  
                        " websocket-client-async-ssl");  
            }));  
  
```

---

## Comment 1

> Username: madmongo1  
> Created at: 2021-06-11 19:17:44 UTC  
> Url: https://github.com/boostorg/beast/issues/2258#issuecomment-859789215  

Using mutexes with async io is almost always an error.  
Have you had a look at asio strands?  
  
https://www.boost.org/doc/libs/1_74_0/doc/html/boost_asio/reference/strand.html

---

## Comment 2

> Username: treyweaver  
> Created at: 2021-06-11 19:50:26 UTC  
> Url: https://github.com/boostorg/beast/issues/2258#issuecomment-859809844  

The problem is not that the mutexs are not working.  The problem is that I am calling .async_write and I never get an on_write event.  And if this happens there does not seem to be any way to recover; the next .async_write you make will give a SIGABRT and the program will crash.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2021-06-12 04:07:00 UTC  
> Updated at: 2021-06-12 04:07:09 UTC  
> Url: https://github.com/boostorg/beast/issues/2258#issuecomment-859994070  

Is this your first asynchronous ASIO program?  
  
Have a look at this https://www.boost.org/doc/libs/1_76_0/doc/html/boost_asio/overview/core/strands.html  
  
You need a "write queue."

---

## Comment 4

> Username: madmongo1  
> Created at: 2021-06-12 10:36:49 UTC  
> Url: https://github.com/boostorg/beast/issues/2258#issuecomment-860034957  

Just to be clear about this, you must not initiate another async_write before the execution of the completion handler of the previous async_write.

---

## Comment 5

> Username: treyweaver  
> Created at: 2021-06-12 15:28:43 UTC  
> Url: https://github.com/boostorg/beast/issues/2258#issuecomment-860068737  

Apparently I have not made my issue clear.    
  
I have packets in a que; I will get an object out of the que and send it using the .async_write command, wait for the handler to get called (in my case it is the on_write function) then get the next object out of the que and repeat.  
  
Once in a while I will do a .async_write and the handler will NEVER get called!  In that case what can I do?  Can I "reset" the websocket somehow and continue?

---

## Comment 6

> Username: madmongo1  
> Created at: 2021-06-12 19:47:57 UTC  
> Url: https://github.com/boostorg/beast/issues/2258#issuecomment-860100337  

Are you able to share a minimal working example that demonstrate the problem please?  
  
this is not behaviour we have observed.

---

## Comment 7

> Username: treyweaver  
> Created at: 2021-06-16 19:12:33 UTC  
> Url: https://github.com/boostorg/beast/issues/2258#issuecomment-862644477  

I changed the architecture so that the async_write was only coming from one thread and the issue went away.    Still not sure what the underlying issue was, but it is working now.  You can close this issue.  Thanks.

---

## Comment 8

> Username: vinniefalco  
> Created at: 2021-06-18 00:09:32 UTC  
> Url: https://github.com/boostorg/beast/issues/2258#issuecomment-863638250  

`async_write` is _not thread safe_.

---

## Comment 9

> Username: stale[bot]  
> Created at: 2022-01-09 03:17:02 UTC  
> Url: https://github.com/boostorg/beast/issues/2258#issuecomment-1008220780  

This issue has been open for a while with no activity, has it been resolved?
