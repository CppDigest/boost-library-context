# #209 - Add timestamp to the logger [Open]

> Username: mzimbres  
> Created at: 2024-08-18 11:47:07 UTC  
> Updated at: 2025-10-22 15:19:38 UTC  
> Url: https://github.com/boostorg/redis/issues/209  

See https://github.com/boostorg/redis/issues/205#issue-2432081055 for motivation.

---

## Comment 1

> Username: anarthal  
> Created at: 2025-10-22 15:19:38 UTC  
> Url: https://github.com/boostorg/redis/issues/209#issuecomment-3432975961  

I'm trying to implement this and I see no way of doing this portably, without dependencies and in C++17. It looks like formatting time_points with `operator<<` requires C++20. And the C library (`strftime` and `localtime`) is not thread-safe.  `localtime_r` is not portable either. I could either do some conditional compilation on the availability of `operator<<`, or leave this as it is.
