# #750 - interprocess build failure in semaphore_timed_wait when passsing boost::posix_time::pos_infin [Open]

> Username: nugins99  
> Created at: 2023-03-12 15:15:43 UTC  
> Updated at: 2023-07-26 11:57:55 UTC  
> Url: https://github.com/boostorg/boost/issues/750  

While investigating a build issue with fast_dds with boost >= 1.77 I discovered that   
  
    template<class TimePoint>   
    inline bool semaphore_timed_wait(sem_t * handle, const TimePoint & abs_time)   
  
in boost/interprocess/sync/posix/semaphore_wrapper.hpp   
  
Fails to compile[with TimePoint = boost::date_time::special_values.    
  
I would expect that is case should compile if the value passed in is "boost::posix_time::pos_infin" as there is a special case in the function:   
  
    if (ipcdetail::is_pos_infinity(abs_time))  { ...}  
  
However,  several lines lower the folloing fails to compile due to a missing overload:   
  
    timespec tspec = timepoint_to_timespec(abs_time);  
  
I am compiling with gcc 11.3.0  on RHEL8 and Pop!_OS 22.04

---

## Comment 1

> Username: wangtingyao  
> Created at: 2023-07-26 11:57:55 UTC  
> Url: https://github.com/boostorg/boost/issues/750#issuecomment-1651664395  

I can compile successfully with gcc 9.4.0 on ubuntu 20.04。Did you？
