# #28 - boost::process::detail::posix::execvpe Is Async Signal Safely ? [Closed]

> Username: fcharlie  
> Created at: 2018-01-18 13:51:13 UTC  
> Updated at: 2018-02-06 09:09:18 UTC  
> Closed at: 2018-02-06 09:09:18 UTC  
> Url: https://github.com/boostorg/process/issues/28  

Deadlock may occur if you execute boost.process code in a multithreaded program  
   
https://github.com/boostorg/process/blob/develop/include/boost/process/detail/posix/executor.hpp#L33-L64  
  
std::string and std::vector require allocate memory. When other thread lock this, child process mybe deadlock.

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2018-02-05 22:18:03 UTC  
> Url: https://github.com/boostorg/process/issues/28#issuecomment-363240430  

I am not sure I follow you here. Those are different processes with differend mempages at this point, why would that lead to a deadlock?

---

## Comment 2

> Username: klemens-morgenstern  
> Created at: 2018-02-05 22:48:54 UTC  
> Url: https://github.com/boostorg/process/issues/28#issuecomment-363248028  

I just remembered, we had another discussion about this issue here: https://github.com/klemens-morgenstern/boost-process/issues/65  
  
I still don't know how to solve that though,

---

## Comment 3

> Username: klemens-morgenstern  
> Created at: 2018-02-06 09:09:17 UTC  
> Url: https://github.com/boostorg/process/issues/28#issuecomment-363357241  

Closing it in favor of the other issue. Will look into it.
