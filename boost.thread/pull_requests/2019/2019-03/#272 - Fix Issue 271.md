# #272 Fix Issue 271 [Merged]

> Username: austin-beer  
> Created at: 2019-03-19 01:30:44 UTC  
> Updated at: 2019-03-25 01:45:15 UTC  
> Merged at: 2019-03-19 05:47:37 UTC  
> Closed at: 2019-03-19 05:47:37 UTC  
> Url: https://github.com/boostorg/thread/pull/272  

* If thread A was waiting to pull the earliest element off of a  
sync_timed_queue, and while it was waiting thread B added a new element with an  
earlier time to the queue, thread A wouldn't reduce how long it waited before  
pulling the earliest element off of the queue.  
  
* Also refactored a function name and a variable name since their names no  
longer accurately described what they do:  
  * notify_not_empty_if_needed() -> notify_elem_added()  
  * not_empty_ -> cond_  
  
* Also deleted a no-longer-used function:  
  * wait_until_closed_until()

---

## Comment 1

> Username: austin-beer  
> Created_at: 2019-03-19 01:35:14 UTC  
> Url: https://github.com/boostorg/thread/pull/272#issuecomment-474163766  

This is the program I used to test this code. It's a modified version of the test code used in Issue 271.  
  
```  
#include <iostream>  
#include "boost/chrono/system_clocks.hpp"  
#include "boost/thread/executors/scheduler.hpp"  
  
int main()  
{  
    boost::executors::scheduler<boost::chrono::steady_clock> sc;  
    auto start = boost::chrono::steady_clock::now();  
    int count = 10;  
    for (int i = 0; i < count; ++i)  
    {  
        boost::chrono::seconds delay(count - i);  
        auto time = boost::chrono::duration_cast<boost::chrono::milliseconds>(boost::chrono::steady_clock::now() - start);  
        std::cout << time << ": Run " << static_cast<char>('A' + i) << " in " << delay << std::endl;  
        sc.submit_after([=] {  
            auto time = boost::chrono::duration_cast<boost::chrono::milliseconds>(boost::chrono::steady_clock::now() - start);  
            std::cout << time << ": Ran " << static_cast<char>('A' + i) << " after " << delay << std::endl;  
        }, delay);  
        boost::this_thread::sleep_for(boost::chrono::milliseconds(500));  
    }  
    boost::this_thread::sleep_for(boost::chrono::seconds(count + 2));  
    return 0;  
}  
```  
  
And here's the output with this fix applied:  
  
```  
0 milliseconds: Run A in 10 seconds  
500 milliseconds: Run B in 9 seconds  
1000 milliseconds: Run C in 8 seconds  
1500 milliseconds: Run D in 7 seconds  
2000 milliseconds: Run E in 6 seconds  
2500 milliseconds: Run F in 5 seconds  
3000 milliseconds: Run G in 4 seconds  
3500 milliseconds: Run H in 3 seconds  
4000 milliseconds: Run I in 2 seconds  
4500 milliseconds: Run J in 1 second  
5500 milliseconds: Ran J after 1 second  
6000 milliseconds: Ran I after 2 seconds  
6500 milliseconds: Ran H after 3 seconds  
7000 milliseconds: Ran G after 4 seconds  
7500 milliseconds: Ran F after 5 seconds  
8000 milliseconds: Ran E after 6 seconds  
8500 milliseconds: Ran D after 7 seconds  
9000 milliseconds: Ran C after 8 seconds  
9500 milliseconds: Ran B after 9 seconds  
10000 milliseconds: Ran A after 10 seconds  
```

---

## Review 2 [Commented]

> Username: austin-beer  
> Created_at: 2019-03-19 01:37:30 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/thread/pull/272#pullrequestreview-215927072  

📁 include/boost/thread/concurrent_queues/sync_timed_queue.hpp

```diff
 230 | 
 231 |       const time_point tp(super::data_.top().time);
 232 |-       super::wait_until_closed_until(lk, tp);
```

> Username: austin-beer  
> Created_at: 2019-03-19 01:37:30 UTC  
> Url: https://github.com/boostorg/thread/pull/272#discussion_r266703003  

The two changes in this file are what actually fix the issue.

> Username: viboes  
> Created_at: 2019-03-19 05:47:02 UTC  
> Url: https://github.com/boostorg/thread/pull/272#discussion_r266738866  

Thanks a lot for the quick fix.


---

## Review 3 [Commented]

> Username: DengZuoheng  
> Created_at: 2019-03-21 09:57:03 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/thread/pull/272#pullrequestreview-217148955  

📁 include/boost/thread/concurrent_queues/detail/sync_queue_base.hpp

```diff
 100 |     {
 103 |-       not_empty_.notify_one();
 101 |+       cond_.notify_all();
```

> Username: DengZuoheng  
> Created_at: 2019-03-21 09:57:03 UTC  
> Url: https://github.com/boostorg/thread/pull/272#discussion_r267684110  

why change to notify all?

> Username: austin-beer  
> Created_at: 2019-03-21 16:26:16 UTC  
> Url: https://github.com/boostorg/thread/pull/272#discussion_r267844739  

Because there may be more than one thread calling wait_to_pull*() and waiting for the next element to be ready. When a new element is added, both threads need to be notified so they can go around the for (;;) loop again and update how long they're waiting for.

> Username: DengZuoheng  
> Created_at: 2019-03-22 02:11:50 UTC  
> Url: https://github.com/boostorg/thread/pull/272#discussion_r268018598  

But seems only sync_timed_queue need to wake up all waiting thread to update new wait_until time point. If we always notify all when push, would it  degrade performance of  sync_queue or others?

> Username: austin-beer  
> Created_at: 2019-03-22 16:04:45 UTC  
> Url: https://github.com/boostorg/thread/pull/272#discussion_r268235827  

There may be a small degradation in performance with notify_all() if there are multiple threads waiting, but if there is only one thread waiting the performance of notify_one() and notify_all() should be about the same. And I still think notify_all() is necessary when there are multiple reader threads waiting to pull an element off of the sync_timed_queue.  
  
Here is what may happen if we use notify_one() instead of notify_all(). Say you have a single writer thread (W) and two reader threads (R1 and R2).  
  
- Time T1: W adds an item to the queue that's set to be pulled off at time T20.  
- Time T2: R1 starts waiting to pull an item off of the queue. It calls cond_.wait_until(T20) and sleeps.  
- Time T3: R2 starts waiting to pull an item off of the queue. It calls cond_.wait_until(T20) and sleeps.  
- Time T4: W adds an item to the queue that's set to be pulled off at time T10. It wakes up **one** thread, and let's assume that it is R1. So R1 goes around the for(;;) loop and calls cond_.wait_until(T10) and sleeps.  
- _Because R2 was never woken up, it is still waiting until time T20 and so has no chance of pulling the first element off of the queue. This is the situation I am trying to avoid. I think that both R1 and R2 should be waiting until time T10 and so both threads will have an equal chance of pulling the first item off of the queue. Does that make sense?_  
  
Perhaps making sure that both R1 and R2 have an equal chance of pulling the first item off of the queue isn't important. I'm open to feedback if you and Vicente think it is not important.

> Username: DengZuoheng  
> Created_at: 2019-03-25 01:45:15 UTC  
> Url: https://github.com/boostorg/thread/pull/272#discussion_r268468026  

OK, understood, thanks a lot for explaining and quick fix of issue.


---
