# #109 - Grammar correction in documentation [Closed]

> Username: aaangeletakis  
> Created at: 2021-03-18 18:00:44 UTC  
> Updated at: 2021-04-23 17:30:59 UTC  
> Closed at: 2021-04-23 17:30:59 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/109  

Section: Handle terminates, aborts and Segmentation Faults  
https://www.boost.org/doc/libs/1_75_0/doc/html/stacktrace/getting_started.html#stacktrace.getting_started.handle_terminates_aborts_and_seg  
  
In the warning it says:  
> Only a few system calls allowed in signal handlers,  
  
The correct way to say this is:  
> Only a few system calls *are* allowed in signal handlers,

---

## Comment 1

> Username: apolukhin  
> Created at: 2021-04-23 17:30:58 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/109#issuecomment-825808390  

Thanks for the report!  
  
That sentence has gone in latest docs https://www.boost.org/doc/libs/1_76_0/doc/html/stacktrace/getting_started.html
