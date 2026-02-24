# #117 - loop_executor should block on it's work_queue instead of polling [Closed]

> Username: Oberon00  
> Created at: 2017-02-27 18:42:03 UTC  
> Updated at: 2017-03-01 07:15:59 UTC  
> Closed at: 2017-02-28 18:55:11 UTC  
> Url: https://github.com/boostorg/thread/issues/117  

`loop_executor::loop` is currently:  
```c++  
    void loop()  
    {  
      while (!closed())  
      {  
        schedule_one_or_yield();  
      }  
      while (try_executing_one())  
      {  
      }  
    }  
```  
  
The first loop repeatedly calls `schedule_one_or_yield()` which is simply  
```c++  
    void schedule_one_or_yield()  
    {  
        if ( ! try_executing_one())  
        {  
          this_thread::yield();  
        }  
    }  
```  
  
The current implementation uses unnecessary CPU cycles (esp. if all other threads are idle) by busy waiting. As `concurrent::sync_queue<work> work_queue` already supports a blocking `wait_pull_front` , it would be better to use a (private?) `wait_executing_one` that behaves just like `try_executing_one` but calls `work_queue.wait_pull_front` instead of `try_pull_front`.  
  
IMHO, the current implementation is an outright performance bug.

---

## Comment 1

> Username: viboes  
> Created at: 2017-02-27 20:33:42 UTC  
> Url: https://github.com/boostorg/thread/issues/117#issuecomment-282844383  

Le 27/02/2017 à 19:42, Christian N. a écrit :  
>  
> |loop_executor::loop| is currently:  
>  
>      void  loop()  
>      {  
>        while  (!closed())  
>        {  
>          schedule_one_or_yield();  
>        }  
>        while  (try_executing_one())  
>        {  
>        }  
>      }  
>  
> The first loop repeatedly calls |schedule_one_or_yield()| which is simply  
>  
>      void  schedule_one_or_yield()  
>      {  
>          if  ( !try_executing_one())  
>          {  
>            this_thread::yield();  
>          }  
>      }  
>  
> The current implementation uses unnecessary CPU cycles (esp. if all   
> other threads are idle) by busy waiting. As   
> |concurrent::sync_queue<work> work_queue| already supports a blocking   
> |wait_pull_front| , it would be better to use a (private?)   
> |wait_executing_one| that behaves just like |try_executing_one| but   
> calls |work_queue.wait_pull_front| instead of |try_pull_front|.  
>  
> IMHO, the current implementation is an outright performance bug.  
>  
Hi,  
  
you are surely right.  
  
Would you mind to add a github issue?  
What about adding a pull request PR?  
  
Vicente

---

## Comment 2

> Username: viboes  
> Created at: 2017-02-27 20:34:57 UTC  
> Url: https://github.com/boostorg/thread/issues/117#issuecomment-282844688  

Sorry. I didn't saw this was already a github issue.  
What about adding a pull request PR?

---

## Comment 3

> Username: Oberon00  
> Created at: 2017-02-27 21:16:07 UTC  
> Url: https://github.com/boostorg/thread/issues/117#issuecomment-282855826  

Ok, will do (but not before tomorrow).
