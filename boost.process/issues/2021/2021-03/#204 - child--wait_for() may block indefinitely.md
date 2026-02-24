# #204 - child::wait_for() may block indefinitely [Open]

> Username: tomix86  
> Created at: 2021-03-24 09:15:02 UTC  
> Updated at: 2021-03-24 09:15:02 UTC  
> Url: https://github.com/boostorg/process/issues/204  

[`child::wait_for()`](https://github.com/boostorg/process/blob/develop/include/boost/process/detail/child_decl.hpp#L184) may block indefinitely if invoked with a short interval (extreme case is 0ns) on a slow machine.  
[`detail::api::wait_for_exit()`](https://github.com/boostorg/process/blob/develop/include/boost/process/detail/windows/wait_for_exit.hpp#L58) called internally calculates wait time as follows (0ns used for the sake of example):  
```  
rel_time = 0ns; // the argument to wait_for()  
timeout_time = Clock::now() + rel_time; // value provided when invoking wait_for_exit() from wait_for()  
ms = duration_cast<milliseconds>(timeout_time - Clock::now())  
```  
  
Therefore on a slow machine, if the time between subsequent calls to `Clock::now()` exceeds 1ms<sup>1</sup>, a negative value would be computed. Such value when passed to [`WaitForSingleObject()`](https://github.com/boostorg/process/blob/develop/include/boost/process/detail/windows/wait_for_exit.hpp#L63) will result in an effectively infinite wait time.  
  
[1]: For shorter time diffs the loss of precision while converting via `std::chrono::duration_cast<std::chrono::milliseconds>` prevents the bug from occurring.
