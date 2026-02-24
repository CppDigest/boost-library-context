# #246 - `boost::posix_time::duration` multiplication/division limited to `int`. [Open]

> Username: JohannesWilde  
> Created at: 2025-09-29 13:00:49 UTC  
> Updated at: 2025-09-29 13:00:49 UTC  
> Url: https://github.com/boostorg/date_time/issues/246  

The `operator*()`, `operator*=()`, `operator/()`, and `operator/=()` currently only support `int` parameters [see  [`boost::posix_time::duration`](https://github.com/boostorg/date_time/blob/85e637cb325208c2af9af791c3a1948b4888c6cd/include/boost/date_time/time_duration.hpp#L204)].  
  
I tried to use it as follows:  
  
```  
std::uint64_t const timedeltaInTensOfNanoseconds = 2793555578;  
boost::posix_time::time_duration const duration = boost::posix_time::nanoseconds(10) * timestamp;  
// duration.total_nanoseconds() = -15014117180  
```  
[I did define `BOOST_DATE_TIME_POSIX_TIME_STD_CONFIG`.]  
  
What did work as expected was:  
  
```  
std::uint64_t const timedeltaInTensOfNanoseconds = 2793555578;  
boost::posix_time::time_duration const duration = boost::posix_time::nanoseconds(10 * timestamp);  
// duration.total_nanoseconds() = 27935555780  
```  
  
Would it be possible to extend the parameter type for above functions to at least support `unsigned` as well? Or even extend it to 64-bit types? Or make them template-methods [with a concept limiting them to `int`s and `float`s]?
