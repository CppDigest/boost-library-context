# #233 - local_microsec_clock::local_time(time_zone_ptr) occasionally returns an incorrect time [Open]

> Username: boimart1  
> Created at: 2023-08-21 14:29:17 UTC  
> Updated at: 2023-08-21 14:29:58 UTC  
> Url: https://github.com/boostorg/date_time/issues/233  

Extremely rarely,  a call to `local_microsec_clock::local_time(time_zone_ptr)` will return a result about 1 second in the past.  
  
On my local machine, running the following code:  
```c++  
#include <boost/date_time/local_time/local_time.hpp>  
#include <iostream>  
  
int main() {  
    boost::local_time::time_zone_ptr zone(new boost::local_time::posix_time_zone("EST-5"));  
  
    for (int i = 0; i < 100'000'000; ++i)  
    {  
        boost::local_time::local_date_time t0 = boost::local_time::local_microsec_clock::local_time(zone);  
        boost::local_time::local_date_time t1 = boost::local_time::local_microsec_clock::local_time(zone);  
        if (t1 < t0)  
        {  
            std::cout << i << ": time went from " << t0 << " to " << t1 << '\n';  
        }  
    }  
  
    return 0;  
}  
```  
produces ~10-15 incorrect results for 100M calls, or one incorrect result per ~7-10M calls.  
  
The output suggests that this always occurs when the second ticks. The fractional part rolls over, but the seconds do not change.  
```  
81899680: time went from 2023-Aug-21 09:17:12.999739 EST to 2023-Aug-21 09:17:12.000740 EST  
91444271: time went from 2023-Aug-21 09:17:27.999755 EST to 2023-Aug-21 09:17:27.000752 EST  
92719444: time went from 2023-Aug-21 09:17:29.999467 EST to 2023-Aug-21 09:17:29.000465 EST  
```  
  
It looks like the issue is caused by the second ticking between the calls to `second_clock::universal_time()` and `second_clock::local_time()`, which causes `utc_offset` to be off by 1 second.  
https://github.com/boostorg/date_time/blob/39714907b7d32ed8f005b5a01d1c2b885b5717b3/include/boost/date_time/microsec_time_clock.hpp#L59-L66  
  
I intend to submit a PR with a solution soon.
