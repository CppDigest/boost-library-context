# #412 - Is this write protected by the lock? [Open]

> Username: setharnold  
> Created at: 2023-03-29 03:10:37 UTC  
> Updated at: 2023-03-29 03:10:37 UTC  
> Url: https://github.com/boostorg/asio/issues/412  

https://github.com/boostorg/asio/blob/46ed570aa36d1a665b0425e26d013e189b26515b/include/boost/asio/detail/conditionally_enabled_mutex.hpp#LL79-L87C6  
  
```  
    // Explicitly release the lock.  
    void unlock()  
    {  
      if (locked_)  
      {  
        mutex_.unlock();  
        locked_ = false;  
      }  
    }  
```  
  
Hello, I'm reviewing a package with Coverity and it points out that this `locked_ = false;` happens after the mutex is unlocked.  
  
Is there something to prevent one thread calling `lock()`, another thread calling `unlock()`, and the order of operations going like this:  
  
```  
mutex_.unlock();  
        mutex_.lock();  
        locked_ = true;  
locked_ = false;  
```  
  
Thanks
