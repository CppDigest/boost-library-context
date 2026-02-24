# #410 - condition_variable::timed_wait incompatible with std::chrono::duration due to lack of is_pos_infinity [Open]

> Username: SpareSimian  
> Created at: 2024-12-04 02:17:56 UTC  
> Updated at: 2024-12-04 03:23:13 UTC  
> Url: https://github.com/boostorg/thread/issues/410  

boost::condition_variable::timed_wait() attempts to invoke wait_duration.is_pos_infinity() on its templated parameter.  
  
The standard doesn't define a member is_pos_infinity expected by timed_wait, forcing one to use a Boost clock rather than an STL one such as steady_clock or system_clock.  
  
https://en.cppreference.com/w/cpp/chrono/duration  
  
Found when compiling with MSVS 2022's STL (line 200 here):  
  
https://github.com/microsoft/STL/blob/main/stl/inc/__msvc_chrono.hpp  
  
A possible workaround would be to define an adapter class that defines this member.

---

## Comment 1

> Username: SpareSimian  
> Created at: 2024-12-04 03:16:57 UTC  
> Url: https://github.com/boostorg/thread/issues/410#issuecomment-2516080180  

I thought perhaps BOOST_THREAD_USE_CHRONO would fix this but the following simple program fails to compile:  
  
```  
#define BOOST_THREAD_USE_CHRONO  
#include <iostream>  
#include <chrono>  
#include <boost/thread/condition_variable.hpp>  
  
int main()  
{  
   return 0;  
}  
  
typedef std::chrono::system_clock Clock;  
typedef Clock::duration TimeDuration;  
typedef Clock::time_point TimePoint;  
  
void foo(boost::mutex& mutex, boost::condition_variable& cv, TimePoint expireTime)  
{  
   boost::unique_lock<boost::mutex> lock(mutex);  
   cv.timed_wait(lock, expireTime);  
}  
```

---

## Comment 2

> Username: SpareSimian  
> Created at: 2024-12-04 03:23:12 UTC  
> Url: https://github.com/boostorg/thread/issues/410#issuecomment-2516087763  

I tried substituting boost::chrono::system_clock and I got the same errors.
