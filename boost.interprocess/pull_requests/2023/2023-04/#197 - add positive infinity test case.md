# #197 add positive infinity test case [Closed]

> Username: janondrusek  
> Created at: 2023-04-01 20:58:52 UTC  
> Updated at: 2023-04-24 21:44:09 UTC  
> Closed at: 2023-04-24 21:44:08 UTC  
> Url: https://github.com/boostorg/interprocess/pull/197  

In my recent effort to upgrade from boost 1.69 I discovered a change of behavior after [the commit 30d5005ac627b36c122e69c86cf518aaaadd92ab](https://github.com/boostorg/interprocess/commit/30d5005ac627b36c122e69c86cf518aaaadd92ab). Is my expectation of `boost::posix_time::pos_infin` to pass the check in [semaphore_wrapper](https://github.com/boostorg/interprocess/blob/develop/include/boost/interprocess/sync/posix/semaphore_wrapper.hpp#L220) incorrect?  
  
One example of a public library relying on `pos_infin` [Fast-DDS](https://github.com/eProsima/Fast-DDS/blob/v2.9.0/src/cpp/utils/shared_memory/RobustInterprocessCondition.hpp#L372)  
  
Attempts to build the library with a recent boost results in:  
  
```  
boost/interprocess/sync/posix/semaphore_wrapper.hpp:226:21: error: no matching function for call to 'timepoint_to_timespec'  
   timespec tspec = timepoint_to_timespec(abs_time);  
                    ^~~~~~~~~~~~~~~~~~~~~  
boost/interprocess/sync/posix/semaphore.hpp:55:14: note: in instantiation of function template specialization 'boost::interprocess::ipcdetail::semaphore_timed_wait<boost::date_time::special_values>' requested here  
   {  return semaphore_timed_wait(&m_sem, abs_time); }  
             ^  
boost/interprocess/sync/interprocess_semaphore.hpp:133:16: note: in instantiation of function template specialization 'boost::interprocess::ipcdetail::posix_semaphore::timed_wait<boost::date_time::special_values>' requested here  
{ return m_sem.timed_wait(abs_time); }  
               ^  
Fast-DDS-2.9.0/src/cpp/utils/shared_memory/RobustInterprocessCondition.hpp:372:45: note: in instantiation of function template specialization 'boost::interprocess::interprocess_semaphore::timed_wait<boost::date_time::special_values>' requested here  
            semaphores_pool_[sem_index].sem.timed_wait(boost::posix_time::pos_infin);  
                                            ^  
boost/interprocess/sync/posix/timepoint_to_timespec.hpp:50:17: note: candidate function not viable: no known conversion from 'const boost::date_time::special_values' to 'const boost::interprocess::ipcdetail::ustime' for 1st argument  
inline timespec timepoint_to_timespec (const ustime &tm)  
                ^  
boost/interprocess/sync/posix/timepoint_to_timespec.hpp:33:17: note: candidate template ignored: substitution failure [with TimePoint = boost::date_time::special_values]: no type named 'type' in 'boost::interprocess::ipcdetail::enable_if_ptime<boost::date_time::special_values>'  
inline timespec timepoint_to_timespec ( const TimePoint &tm  
                ^  
boost/interprocess/sync/posix/timepoint_to_timespec.hpp:59:17: note: candidate template ignored: substitution failure [with TimePoint = boost::date_time::special_values]: no type named 'type' in 'boost::interprocess::ipcdetail::enable_if_time_point<boost::date_time::special_values>'  
inline timespec timepoint_to_timespec ( const TimePoint &tm  
                ^  
```  
  
I attempted to add a repro case. What would be the best way to approach a fix? Thanks!

---

## Comment 1

> Username: igaztanaga  
> Created_at: 2023-04-24 21:44:08 UTC  
> Url: https://github.com/boostorg/interprocess/pull/197#issuecomment-1520869709  

Support for pos_inf was not intended in the library (it was not even tested), but it seems that posix_ptime has a converting constructor for pos_inf and the trick worked. When the interface was templated to support different time libraries (std, boost, date_time), then the converting constructor does not kick in. Adding support for pos_inf and friends would make again Interprocess dependent on date-time which, something that we want to avoid. I've tried some workarounds but the solution does not seem plausible or at least easy. So I'm afraid that support for pos_inf was lost in the transition creating an unintended breaking change...  
  
Strangely, [Fast-DDS](https://github.com/eProsima/Fast-DDS/blob/v2.9.0/src/cpp/utils/shared_memory/RobustInterprocessCondition.hpp#L372) is using a timed function with the infinite timeout to implement a blocking lock. I can suggest changing  
  
`semaphores_pool_[sem_index].sem.timed_wait(boost::posix_time::pos_infin);`  
  
with   
  
`semaphores_pool_[sem_index].sem.timed_wait(boost::posix_time::ptime(boost::posix_time::pos_infin));`  
  
that should work with all versions of Boost.Interprocess

---
