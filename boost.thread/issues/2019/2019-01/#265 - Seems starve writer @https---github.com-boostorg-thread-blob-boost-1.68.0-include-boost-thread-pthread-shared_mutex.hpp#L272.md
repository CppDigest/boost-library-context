# #265 - Seems starve writer @https://github.com/boostorg/thread/blob/boost-1.68.0/include/boost/thread/pthread/shared_mutex.hpp#L272 [Closed]

> Username: DengZuoheng  
> Created at: 2019-01-08 08:34:49 UTC  
> Updated at: 2022-06-20 20:38:57 UTC  
> Closed at: 2019-03-25 01:59:38 UTC  
> Url: https://github.com/boostorg/thread/issues/265  

~~~  
        void lock()  
        {  
#if defined BOOST_THREAD_PROVIDES_INTERRUPTIONS  
            boost::this_thread::disable_interruption do_not_disturb;  
#endif  
            boost::unique_lock<boost::mutex> lk(state_change);  
            state.exclusive_waiting_blocked=true;  
            exclusive_cond.wait(lk, boost::bind(&state_data::can_lock, boost::ref(state)));  
            state.exclusive=true;  
        }  
~~~  
In last reader unlock_shared, the `state.exclusive_waiting_blocked` will set to false, if there are writer and reader waiting at that time and reader wake up firstly and get a read-lock, then the writer need to wait again, but the `state.exclusive_waiting_blocked` would not set to true again for waiting writer.   
  
Would it starve writer?

---

## Comment 1

> Username: viboes  
> Created at: 2019-03-17 15:16:21 UTC  
> Url: https://github.com/boostorg/thread/issues/265#issuecomment-473675225  

I'll need sometime to refresh my memory.

---

## Comment 2

> Username: austin-beer  
> Created at: 2019-03-23 20:47:23 UTC  
> Url: https://github.com/boostorg/thread/issues/265#issuecomment-475903438  

If I understand correctly, here is the scenario that @DengZuoheng has described.  
  
R1 and R2 are both reader threads. W is a writer thread.  
  
- R1 is holding a shared lock (it is the only thread holding a shared lock so shared_count is 1).  
- W wants to get an exclusive lock. It enters the lock() function, sets exclusive_waiting_blocked to true, and then waits on exclusive_cond.  
- R1 calls unlock_shared(). That sets shared_count to 0 and exclusive_waiting_blocked to false, and then calls notify_one() on exclusive_cond.  
- R2 wakes up and calls lock_shared(). Since exclusive_waiting_blocked is false, the call succeeds without blocking and R2 now holds a shared lock.  
- W wakes up due to the notify_one() call but sees that shared_count is still 1 and so keeps waiting, but without setting exclusive_waiting_blocked back to true. Writer starvation occurs.  
  
I think this situation is possible. However, I think it is extremely unlikely due to the fact that it requires R2 to call lock_shared() and be scheduled by the OS right between the time R1 calls unlock_shared() and the time W wakes up.  
  
Boost.Thread contains another [implementation of shared_mutex.hpp](https://github.com/boostorg/thread/blob/develop/include/boost/thread/v2/shared_mutex.hpp) that was originally created by Howard Hinnant to replace the existing pthread/shared_mutex.hpp. This implementation can be used by building Boost and your application with BOOST_THREAD_V2_SHARED_MUTEX. I believe this implementation is simpler and more robust than the existing implementation. However, without a set of tests to prove that, it's probably too risky to replace the existing implementation that has been in use for years.  
  
This V2 implementation would behave as follows in the above scenario.  
  
- R1 is holding a shared lock (it is the only thread holding a shared lock so num_readers is 1).  
- W wants to get an exclusive lock. It enters the lock() function, sets write_entered_ to true, and then waits on gate2_.  
- R1 calls unlock_shared(). That sets num_readers to 0 and then calls notify_one() on gate2_.  
- R2 wakes up and calls lock_shared(). Since write_entered_ is still true, it blocks and starts waiting on gate1_.  
- W wakes up due to the notify_one() call and sees that num_readers is 0 and so it successfully takes the lock. No starvation occurs.

---

## Comment 3

> Username: viboes  
> Created at: 2019-03-24 08:07:11 UTC  
> Url: https://github.com/boostorg/thread/issues/265#issuecomment-475937412  

I'm really sorry. I believed that BOOST_THREAD_V2_SHARED_MUTEX was defined when you define BOOST_THREAD_VERSION>=3 and it seems it is not the case.  
  
Please, go ahead and try the V2 version by defining BOOST_THREAD_V2_SHARED_MUTEX and let us know if you are yet suffering from the same symptoms.  
  
Thanks Austin for your help.

---

## Comment 4

> Username: DengZuoheng  
> Created at: 2019-03-25 01:59:38 UTC  
> Url: https://github.com/boostorg/thread/issues/265#issuecomment-476029103  

Thanks @austin-beer and @viboes  for help, I will try shared_mutex v2, I will open new issue if I  still suffering same symptoms.

---

## Comment 5

> Username: AbdoSalem  
> Created at: 2021-10-05 11:54:46 UTC  
> Updated at: 2021-10-05 12:14:49 UTC  
> Url: https://github.com/boostorg/thread/issues/265#issuecomment-934339477  

Just to confirm that the situation described in https://github.com/boostorg/thread/issues/265#issuecomment-475903438 with the non v2 shared mutex, is reproducible. We have a situation where a unique lock starves in ~ 1 out of 5 times. The situation we have that increases the likelihood of the occurence of this issue is as follows:  
- ~100 threads request  shared locks all the time, due to internal logic they can wait for one another  
-  another thread requests the unqiue lock, so it waits until the shared locks are done   
- the ~100 threads keep requesting the shared locks and they get it before the unique lock does, hence the unique lock starves.     
  
This is just to reply to @austin-beer  comment   
`I think this situation is possible. However, I think it is extremely unlikely due to the fact that it requires R2 to call lock_shared() and be scheduled by the OS right between the time R1 calls unlock_shared() and the time W wakes up.`  
  
The above scenario was reproduced using 1.70. Previously, we did not experience such an issue but then we were on 1.58. We see that the above bhavior has changed on 1.67 and unfortunately there was no mention of it in the release notes.

---

## Comment 6

> Username: JSoet  
> Created at: 2022-06-20 20:38:56 UTC  
> Url: https://github.com/boostorg/thread/issues/265#issuecomment-1160830693  

I'm not sure, but I think the example mentioned here:  
https://github.com/boostorg/thread/issues/361  
Is the same issue, of writer starvation, and he included some sample code there to reproduce the problem. He also included a link to a commit which he said seems to have introduced the issue in boost 1.68
