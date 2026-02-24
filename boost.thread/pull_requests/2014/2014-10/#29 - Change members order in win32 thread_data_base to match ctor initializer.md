# #29 Change members order in win32 thread_data_base to match ctor initializer... [Merged]

> Username: awulkiew  
> Created at: 2014-10-22 22:07:06 UTC  
> Updated at: 2014-10-23 10:11:50 UTC  
> Merged at: 2014-10-22 22:39:04 UTC  
> Closed at: 2014-10-22 22:39:04 UTC  
> Url: https://github.com/boostorg/thread/pull/29  

... list.  
  
The purpose of this PR is to silence the compiler warnings.  
  
I wasn't sure which member should go first, id or tss_data. I've choosen the smaller one but the first should probably be the one which may be used more often.

---
