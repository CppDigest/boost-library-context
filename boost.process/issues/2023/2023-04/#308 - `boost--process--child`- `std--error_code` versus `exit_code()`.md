# #308 - `boost::process::child`: `std::error_code` versus `exit_code()` [Open]

> Username: HunterZ  
> Created at: 2023-04-23 23:11:25 UTC  
> Updated at: 2023-06-13 13:10:00 UTC  
> Url: https://github.com/boostorg/process/issues/308  

Most of the `boost::process::child` API provides `std::error_code` values, but it's only possible to get the final process exit code as `int`. What is the reason for this dichotomy?  
  
Is there any overlap, or is it that the `std::error_code` is only for reporting errors that occur during Boost/C++/OS processing, while `exit_code()` is only for reporting the exit code returned by a child process that exited of its own accord?

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2023-06-13 13:10:00 UTC  
> Url: https://github.com/boostorg/process/issues/308#issuecomment-1589282901  

That's on purpose as there is no universal error category for exit-codes.   
  
What does a process exiting with code `42` mean?
