# #399 - condition_variable_any stuck waiting [Open]

> Username: JulesRapanga  
> Created at: 2024-03-09 01:34:03 UTC  
> Updated at: 2024-03-09 01:34:03 UTC  
> Url: https://github.com/boostorg/thread/issues/399  

Boost 1.83.0  
MSVC 19.38.33135.0  
  
Occasionally I hit an issue where a `boost::condition_variable_any` gets stuck during `wait()` with a predicate, where I know that the predicate evaluates to `true` and `notify_one()` has definitely been called multiple times. All that I have to go on is a core dump which as well as showing the above shows that  
* the thread running `wait()` is in `interruptible_wait`  
* `total_count` is 0  
  
So presumably `total_count` has been decremented in `wake_waiters` but `winapi::ReleaseSemaphore` hasn't resulted in the waiting thread resuming. `ReleaseSemaphore` officially can fail but `condition_variable`'s usage doesn't check the return code. Since `total_count` is zero subsequent calls to `notify` will have no effect.   
![stuck_boost_cv](https://github.com/boostorg/thread/assets/19490002/5b86c19f-d43d-4737-b425-e35f6e47417c)  
  
The same app code runs on linux without hitting this issue.
