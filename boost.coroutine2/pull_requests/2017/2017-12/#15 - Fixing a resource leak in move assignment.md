# #15 Fixing a resource leak in move assignment [Merged]

> Username: hamparawa  
> Created at: 2017-12-20 05:52:52 UTC  
> Updated at: 2017-12-20 06:39:37 UTC  
> Merged at: 2017-12-20 06:24:20 UTC  
> Closed at: 2017-12-20 06:24:20 UTC  
> Url: https://github.com/boostorg/coroutine2/pull/15  

When a coroutine is move assigned to another coroutine, control block of source is set to nullptr.   
This results in a leak (in order of kilobytes/megabytes depending on the stack size) when the moved temporary is destructed, the destructor not invoking the deallocate() method as the control block is nullptr.  
  
This fix attempts to solve the issue by swapping the control blocks, and as the rvalue's destructor is called, the resources are freed.

---

## Comment 1

> Username: olk  
> Created_at: 2017-12-20 06:24:53 UTC  
> Url: https://github.com/boostorg/coroutine2/pull/15#issuecomment-352976330  

ty, I've applied the modifications to the specializations too.

---

## Comment 2

> Username: hamparawa  
> Created_at: 2017-12-20 06:27:48 UTC  
> Url: https://github.com/boostorg/coroutine2/pull/15#issuecomment-352976758  

By the look of it, the same issue seems to have in the move constructors as well.

---

## Comment 3

> Username: olk  
> Created_at: 2017-12-20 06:39:37 UTC  
> Url: https://github.com/boostorg/coroutine2/pull/15#issuecomment-352978512  

right ... done

---
