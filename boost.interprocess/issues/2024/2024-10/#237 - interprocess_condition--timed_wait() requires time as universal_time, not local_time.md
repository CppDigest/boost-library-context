# #237 - interprocess_condition::timed_wait() requires time as universal_time, not local_time [Closed]

> Username: Tails86  
> Created at: 2024-10-28 16:10:47 UTC  
> Updated at: 2024-11-18 12:47:53 UTC  
> Closed at: 2024-11-18 12:47:52 UTC  
> Url: https://github.com/boostorg/interprocess/issues/237  

Boost version: 1.86.0  
OS: Linux, Ubuntu 22.04  
  
I'm used to using ` boost::posix_time::microsec_clock::local_time()` or `std::chrono::steady_clock::now()` as my time base for most boost timed wait functionality. However, it seems that `interprocess_condition::timed_wait()` returns false immediately for me when I have a time 1 second in the future (I am in UTC-7 time zone). Things start to work fine if I either adjust my system time to UTC+0 or update the code to instead use `boost::posix_time::microsec_clock::universal_time()` as my time base.

---

## Comment 1

> Username: Tails86  
> Created at: 2024-10-28 16:29:43 UTC  
> Updated at: 2024-10-28 17:03:34 UTC  
> Url: https://github.com/boostorg/interprocess/issues/237#issuecomment-2442068985  

If this is instead a misunderstanding on my end, I would like to see the [documentation](https://www.boost.org/doc/libs/1_48_0/doc/html/boost/interprocess/interprocess_condition.html#id977482-bb) updated to clarify this.   
  
The one area I have played around with where the type of clock used didn't seem to matter was with `boost::asio::io_service::run_until()` where I use an offset from `std::chrono::steady_clock::now()`. Since this is all a part of boost, I assumed I could just reference this code for interprocess code. Now that I'm looking more closely, `boost::asio::io_service::run_until()` uses `const chrono::time_point< Clock, Duration > & abs_time`, and `interprocess_condition::timed_wait()` uses `const boost::posix_time::ptime & abs_time`.

---

## Comment 2

> Username: igaztanaga  
> Created at: 2024-11-12 20:38:51 UTC  
> Updated at: 2024-11-12 20:40:50 UTC  
> Url: https://github.com/boostorg/interprocess/issues/237#issuecomment-2471536684  

This is mentioned in the documentation:  
  
https://www.boost.org/doc/libs/1_86_0/doc/html/interprocess/synchronization_mechanisms.html#interprocess.synchronization_mechanisms.mutexes.mutexes_scoped_lock  
  
"Boost.Interprocess timed synchronization operations support several timepoints: boost::posix_time::ptime absolute UTC time points, boost::chrono and std::chrono timepoints"  
  
Also in chapter:  
  
https://www.boost.org/doc/libs/1_86_0/doc/html/interprocess/synchronization_mechanisms.html#interprocess.synchronization_mechanisms.mutexes.mutexes_mutex_operations  
  
Maybe it's not visible enough and this should be placed in a general section.

---

## Comment 3

> Username: Tails86  
> Created at: 2024-11-15 16:59:29 UTC  
> Url: https://github.com/boostorg/interprocess/issues/237#issuecomment-2479437588  

The more I look around, the more it seems to me like this is a well-known thing that I just missed because I was mainly looking at the class definition documentation itself. I wouldn't feel upset if this is deemed as something that is documented enough based on the links provided.

---

## Comment 4

> Username: igaztanaga  
> Created at: 2024-11-18 12:47:52 UTC  
> Url: https://github.com/boostorg/interprocess/issues/237#issuecomment-2482948481  

Thanks for the feedback, closing the issue.
