# #363 posix: Fix exec error reporting with limit_handles [Merged]

> Username: dkl  
> Created at: 2024-04-17 14:50:44 UTC  
> Updated at: 2024-06-04 00:19:55 UTC  
> Merged at: 2024-06-04 00:19:54 UTC  
> Closed at: 2024-06-04 00:19:54 UTC  
> Url: https://github.com/boostorg/process/pull/363  

Hi,  
  
This pull request contains a fix for posix `limit_handles`: It looks like `_pipe_sink` (the internal pipe used for passing exec() errors from child to parent) must be assigned earlier, before `call_on_setup()` and `limit_fd_::on_setup()`, so `executor::get_used_handles()` can see it. Otherwise it will be closed when using `limit_handles`, despite commit 1a1d677d which added the `executor::get_used_handles()`.

---
