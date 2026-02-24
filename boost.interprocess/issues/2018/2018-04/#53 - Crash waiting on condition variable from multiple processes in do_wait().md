# #53 - Crash waiting on condition variable from multiple processes in do_wait() [Closed]

> Username: JoeStaines  
> Created at: 2018-04-13 12:46:58 UTC  
> Updated at: 2018-04-29 10:52:11 UTC  
> Closed at: 2018-04-29 10:52:11 UTC  
> Url: https://github.com/boostorg/interprocess/issues/53  

I believe i have also ran into the issue described in this stack overflow post.  
  
https://stackoverflow.com/questions/35305291/boost-interprocess-condition-multiple-threads-calling-wait-fails  
  
Is the official stance to just use fixed addess mapping or can this issue be addressed?

---

## Comment 1

> Username: igaztanaga  
> Created at: 2018-04-29 10:52:11 UTC  
> Url: https://github.com/boostorg/interprocess/issues/53#issuecomment-385242545  

Disabled Posix process-shared support as it's buggy. Fixed in:  
  
https://github.com/boostorg/interprocess/commit/911c79e5b5e21824683acda2cedf3195dfb9a338  
  
Many thanks for the report.
