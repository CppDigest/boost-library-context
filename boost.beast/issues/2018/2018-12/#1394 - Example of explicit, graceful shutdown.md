# #1394 - Example of explicit, graceful shutdown [Open]

> Username: vinniefalco  
> Created at: 2018-12-29 03:45:51 UTC  
> Updated at: 2019-07-16 18:47:35 UTC  
> Url: https://github.com/boostorg/beast/issues/1394  

A new example, or the advanced examples, should demonstrate a clean shutdown with a grace period. It could be triggered by the first SIGINT, or perhaps by an HTTP GET request from the loopback address to a special URL (e.g. "GET /cgi-bin/stop HTTP/1.1").  
  
See: https://github.com/ripple/rippled/commit/9ad2b9be45e768dc2bb088cf9fae71eaf11a7349

---

## Comment 1

> Username: benstadin  
> Created at: 2019-07-16 14:51:03 UTC  
> Url: https://github.com/boostorg/beast/issues/1394#issuecomment-511850384  

I'm interested in an example to gracefully shutdown a Beast based server. Background (long story short) for my case is that I use catch2 in a cross platform project, on OS X with XCode this requires some workarounds and force me to start / stop the server for test cases.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2019-07-16 14:54:35 UTC  
> Url: https://github.com/boostorg/beast/issues/1394#issuecomment-511851928  

Here's one example  
https://github.com/vinniefalco/BeastLounge/blob/develop/server/server.cpp#L365

---

## Comment 3

> Username: benstadin  
> Created at: 2019-07-16 17:00:53 UTC  
> Url: https://github.com/boostorg/beast/issues/1394#issuecomment-511901317  

Thanks for the quick reply! Starting to check port this. Though it would be nice if that behavior was part of the default implementation.

---

## Comment 4

> Username: benstadin  
> Created at: 2019-07-16 17:39:27 UTC  
> Url: https://github.com/boostorg/beast/issues/1394#issuecomment-511915043  

It works for me now, but I intentionally did not implement the server_impl class for maintenance reasons. My current implementation is based on the http async server example. I added a close method to the listener like below to shutdown the io context and the acceptor:  
  
```  
    void close() {  
        if (!_stop) {  
            _stop = true;  
            ioc_.stop();  
            acceptor_.close();  
        }  
    }  
```  
  
The server is handling the shutdown similar to your example, but calls to close the listener like this:  
  
```  
// Block the main thread until stop() is called  
    {  
        std::unique_lock<std::mutex> lock(mutex_);  
        cv_.wait(lock, [] {  
            return stop_.load();  
        });  
    }  
      
    listenerInstance->close();  
      
    // services must be kept alive until after  
    // all executor threads are joined.  
      
    // If we get here, then the server has  
    // stopped, so join the threads before  
    // destroying them.  
      
    for(auto& t : v)  
        t.join();  
```  
  
The stop() method looks same as in your example, just without the signal handling.   
  
Is this ok to do it this way?

---

## Comment 5

> Username: vinniefalco  
> Created at: 2019-07-16 18:47:35 UTC  
> Url: https://github.com/boostorg/beast/issues/1394#issuecomment-511939310  

> Is this ok to do it this way?  
  
I/O objects are not thread safe. You have to post to the strand or executor if you are calling `acceptor_.close()`, and `acceptor_` is being accessed from another thread.  
  
However, since you are calling `ioc_.stop();`, you don't need to close `acceptor_` at all, just let it get destroyed normally.
