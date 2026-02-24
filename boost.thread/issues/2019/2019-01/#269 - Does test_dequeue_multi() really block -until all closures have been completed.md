# #269 - Does test_dequeue_multi() really block "until all closures have been completed"? [Closed]

> Username: dkrejsa  
> Created at: 2019-01-30 00:40:41 UTC  
> Updated at: 2019-04-13 07:10:53 UTC  
> Closed at: 2019-04-13 07:10:53 UTC  
> Url: https://github.com/boostorg/thread/issues/269  

The test_scheduled_tp.cpp test has some sub-cases that spawn a group of threads, each of which  
submit delayed work to a scheduled_thread_pool.  One of the functions used, test_dequeue_multi(),  
ends with a comment  
  
        tg.join_all();  
        //dtor is called here so execution will block untill all the closures  
        //have been completed.  
    }  
  
that suggests to me that it is meant to block until all of the previously scheduled delayed work has completed.  However, it appears that what actually happens is that the function returns immediately after the tg.join_all(), so that only the jobs that were scheduled with zero-duration delay actually run (maybe -- it might be a race?); the scheduled_thread_pool 'se' is immediately destructed and does not wait for the delayed jobs to complete.  
  
Is this a correct understanding, and is this the intent of the test?

---

## Comment 1

> Username: viboes  
> Created at: 2019-03-17 15:14:11 UTC  
> Url: https://github.com/boostorg/thread/issues/269#issuecomment-473675035  

I'll need some time to refresh my memory.

---

## Comment 2

> Username: viboes  
> Created at: 2019-04-13 07:10:32 UTC  
> Url: https://github.com/boostorg/thread/issues/269#issuecomment-482783678  

Unfortunately, there is not documentation for this class/file.  
  
I don't remember what was the intent of the test, but I believe the intent of the scheduled_thread_pool destructor was to avoid any additional work, interrupt all the on going tasks and wait until these tasks finish.  
  
An alternative  could be to let wait fo rall all the delayed task to start, but the idea was to finish as soon as possible, with the collaboration of the on going tasks, not to wait until all the ongoing and delayed task finish.  
  
If you need another behavior you will need to define your own scheduler that do something else on the destructor.  
  
A PR is welcome with this new kind of scheduler.
