# #239 - basic_waitable_timer spuriously fires expired callbacks [Closed]

> Username: romange  
> Created at: 2019-05-16 15:20:59 UTC  
> Updated at: 2019-05-16 19:28:28 UTC  
> Closed at: 2019-05-16 19:28:27 UTC  
> Url: https://github.com/boostorg/asio/issues/239  

For some reason, asio context sometimes fires timer callbacks right after they were armed with `operation_aborted` error code. See the snippet below:  
  
```  
chrono::steady_clock::time_point const abs_time = ...  
suspend_timer_->expires_at(abs_time);  
suspend_timer_->async_wait([ abs_time](const system::error_code& ec) {  
   VLOG(1) << "Fire callback " << abs_time.time_since_epoch().count() << " " << ec;  
});  
  
VLOG(1) << "Arm callback at micros from now " << delta_micros(abs_time) << ", abstime: "  
               << abs_time.time_since_epoch().count();  
  
... continue with io run loop.  
```  
The log:  
```  
I0516 15:04:14.902227 Arm callback at micros from now 9981, abstime: 6392942606157701  
  
I0516 15:04:14.902307 Fire callback 6392942606157701 system:125  
```  
  
What can be the reason for this?

---

## Comment 1

> Username: romange  
> Created at: 2019-05-16 15:22:31 UTC  
> Url: https://github.com/boostorg/asio/issues/239#issuecomment-493113440  

```  
inline int64_t delta_micros(const chrono::steady_clock::time_point tp) {  
  return chrono::duration_cast<chrono::microseconds>(tp - chrono::steady_clock::now()).count();  
}  
```

---

## Comment 2

> Username: djarek  
> Created at: 2019-05-16 19:21:55 UTC  
> Url: https://github.com/boostorg/asio/issues/239#issuecomment-493198882  

Are you rearming the timer after there was an `async_wait` started on it?

---

## Comment 3

> Username: romange  
> Created at: 2019-05-16 19:28:27 UTC  
> Url: https://github.com/boostorg/asio/issues/239#issuecomment-493200854  

Yes, thank you. I've missed another call path to that timer that rearms it. Closing the issue.
