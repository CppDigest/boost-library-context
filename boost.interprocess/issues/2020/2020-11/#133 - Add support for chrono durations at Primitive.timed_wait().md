# #133 - Add support for [std/boost]chrono durations at Primitive.timed_wait() [Closed]

> Username: leha-bot  
> Created at: 2020-11-27 15:46:57 UTC  
> Updated at: 2021-08-21 22:34:15 UTC  
> Closed at: 2021-08-21 22:34:15 UTC  
> Url: https://github.com/boostorg/interprocess/issues/133  

It would be very convenient to add the standard std::chrono::duration() support into all primitives supporting timed_wait() in `wait_for()` form (like similar methods in Standard's primitives, e.g. [std::condition_variable::wait_for](https://en.cppreference.com/w/cpp/thread/condition_variable/wait_for)).  
  
Thanks in advance,  
Best regards,  
Alex.

---

## Comment 1

> Username: igaztanaga  
> Created at: 2021-08-21 22:34:15 UTC  
> Url: https://github.com/boostorg/interprocess/issues/133#issuecomment-903184071  

Standard wait_until/wait_for interface was added in the recent commit, to be released in Boost 1.78:  
  
https://github.com/boostorg/interprocess/commit/512f95cb075367d2c648bbfa494c11d04b5273fe  
  
Thanks for the report and sorry for the long delay
