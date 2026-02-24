# #70 Guard against EINTR in basic_pipe [Merged]

> Username: thomassuckow  
> Created at: 2019-01-22 17:23:35 UTC  
> Updated at: 2019-11-22 05:01:53 UTC  
> Merged at: 2019-11-22 05:01:52 UTC  
> Closed at: 2019-11-22 05:01:52 UTC  
> Url: https://github.com/boostorg/process/pull/70  

Based on the code from executor.hpp, a signal can cause invoking a child process to fail because reading/writing the pipe was interrupted.

---
