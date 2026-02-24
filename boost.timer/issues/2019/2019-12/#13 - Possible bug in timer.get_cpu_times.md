# #13 - Possible bug in timer.get_cpu_times [Closed]

> Username: Davack  
> Created at: 2019-12-10 20:42:03 UTC  
> Updated at: 2023-04-19 22:08:36 UTC  
> Closed at: 2023-04-19 22:08:35 UTC  
> Url: https://github.com/boostorg/timer/issues/13  

Hi,  
  
### Problem  
I've noticed that using `boost::timer::cpu_times` function is not context switch secured.  
Since `boost::timer::cpu_times` is implemented in a way that calls (in UNIX environments) two syscalls to calculate the `boost::timer::cpu_times` so there is a chance that context switch will occur between both syscalls.  
The measurements could be corrupted since a context switch could occur between `current.wall` declaration and `current.system` or `current.user` declarations.   
  
the syscalls called inside `timer::get_cpu_times`:  
1. Calling `boost::chrono::high_resolution_clock::now()` ([source](https://github.com/boostorg/timer/blob/master/src/cpu_timer.cpp#L119-L120)) cause `clock_gettime` syscall to invoke ([system clock case](https://github.com/boostorg/chrono/blob/1f590d694ba4544a27d768db0fa4e6cbe9425200/include/boost/chrono/detail/inlined/posix/chrono.hpp#L38) and [steady clock case](https://github.com/boostorg/chrono/blob/1f590d694ba4544a27d768db0fa4e6cbe9425200/include/boost/chrono/detail/inlined/posix/chrono.hpp#L86)).  
2. Calling `times` syscall in order to retrieve process CPU time ([source](https://github.com/boostorg/timer/blob/master/src/cpu_timer.cpp#L140)).  
  
### Suggestion  
In order to make `boost::timer::cpu_times` safe from context switches, we should invoke `times` syscall to retrieve both process CPU time and current time (as returned from syscall return value).  
This way, we're using only one syscall in order to calculate our CPU usage and since UNIX kernel is monolithic, it's guaranteed that no context switches will occur.  
(`times` [man page](https://linux.die.net/man/2/times))

---

## Comment 1

> Username: pdimov  
> Created at: 2023-04-19 22:08:35 UTC  
> Url: https://github.com/boostorg/timer/issues/13#issuecomment-1515446946  

Thanks for the suggestion; implemented in https://github.com/boostorg/timer/commit/1a53ea1d8ec0e4eeeda9d2f883ed057adba8a392. Sorry it took so long.
