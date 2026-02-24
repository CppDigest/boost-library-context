# #255 - Inaccuracy in basic_waitable_timer on virtualized macos [Closed]

> Username: mellery451  
> Created at: 2019-06-21 22:35:37 UTC  
> Updated at: 2020-12-30 01:06:31 UTC  
> Closed at: 2020-12-30 01:06:30 UTC  
> Url: https://github.com/boostorg/asio/issues/255  

I'm seeing substantial inaccuracy in `basic_waitable_timer` on a virtualized macos environment (at Travis-CI). Specifically, I set a 100ms expiration and measure the actual time to callback. On the same provider using linux, I see consistent results (100 samples):  
  
```  
========================================  
Mean: 100ms  
Median: 100ms  
Max: 100ms  
Min: 100ms  
========================================  
```  
  
whereas on the macos system:  
```  
========================================  
Mean: 157.42ms  
Median: 161ms  
Max: 248ms  
Min: 100ms  
========================================  
```  
  
What factors should I look at to understand this difference? Is the underlying kqueue system sensitive to CPU timings or other factors impacted by virtualization?  
  
For reference, the code I"m running is  [here](https://github.com/mellery451/asio_timer_test/blob/master/asio_timer_test.cpp) and typical results can be see in [this travis run](https://travis-ci.org/mellery451/asio_timer_test/builds/548891749)  
  
Any advice appreciated.

---

## Comment 1

> Username: mellery451  
> Created at: 2019-06-24 17:42:50 UTC  
> Url: https://github.com/boostorg/asio/issues/255#issuecomment-505109839  

FWIW, I made a build with handler tracking enabled and you can see the same inconsistency in the timer expiration based on the timestamps:  https://travis-ci.org/mellery451/asio_timer_test/jobs/549851983

---

## Comment 2

> Username: ghost  
> Created at: 2020-12-30 01:06:29 UTC  
> Url: https://github.com/boostorg/asio/issues/255#issuecomment-752292469  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#702](https://github.com/chriskohlhoff/asio/issues/702).
