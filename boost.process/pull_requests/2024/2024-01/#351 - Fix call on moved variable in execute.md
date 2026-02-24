# #351 Fix call on moved variable in execute [Merged]

> Username: DevonMorris  
> Created at: 2024-01-11 14:48:14 UTC  
> Updated at: 2024-03-31 22:38:00 UTC  
> Merged at: 2024-03-31 22:38:00 UTC  
> Closed at: 2024-03-31 22:38:00 UTC  
> Url: https://github.com/boostorg/process/pull/351  

When `async_execute` is called on a process, the process is moved into a unique pointer and then the executor is called on the moved from process. This causes warnings from many static analyzers and compilers.  
  
Although in this case I don't believe this is a bug, since it appears that the executor is being copied (from what I can tell), it may be a good idea to simply call this method on the new object.

---
