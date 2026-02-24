# #362 - Possibly wrong readers count in boost::thread_v2::upgrade_mutex [Open]

> Username: daniel-boehme  
> Created at: 2021-11-05 14:50:56 UTC  
> Updated at: 2021-11-05 14:50:56 UTC  
> Url: https://github.com/boostorg/thread/issues/362  

Because of issues (https://github.com/boostorg/thread/issues/265, https://github.com/boostorg/thread/issues/361) in the default shared mutex implementation, I reviewed the code of the V2 mutex classes.  
  
It seems like `upgrade_mutex::try_unlock_shared_and_lock_until(const boost::chrono::time_point<Clock, Duration>& abs_time)` and `upgrade_mutex::try_unlock_upgrade_and_lock_until(const boost::chrono::time_point<Clock, Duration>& abs_time)` can set the readers count to a wrong value.  
  
Both functions contain the same piece of code:  
```  
      count_t num_readers = (state_ & n_readers_) - 1;  
      state_ &= ~n_readers_;  
      state_ |= (write_entered_ | num_readers);  
      if (!gate2_.wait_until(lk, abs_time, boost::bind(  
            &upgrade_mutex::no_readers, boost::ref(*this))))  
      {  
        ++num_readers;  
        state_ &= ~(write_entered_ | n_readers_);  
        state_ |= num_readers;  
```  
  
The problem is with `++num_readers`. Between reading it out of the state and then setting it again, the mutex is unlocked, so readers can `unlock_shared()`. This is even necessary, as the code actually waits until the readers count reaches 0.  
  
So IMHO, the line in question should read:  
```  
num_readers = (state_ & n_readers_) +1;  
```
