# #123 - Assertion failure in schedule_from_remote [Closed]

> Username: laqrix  
> Created at: 2017-05-08 19:33:36 UTC  
> Updated at: 2017-05-26 16:21:55 UTC  
> Closed at: 2017-05-26 16:21:55 UTC  
> Url: https://github.com/boostorg/fiber/issues/123  

Under Boost 1.64 and 1.63 using this boiled down example on Windows  
(VS2015) when the condition is signaled, inside  
`scheduler::schedule_from_remote(ctx)`, the assertion  
`!ctx->sleep_is_linked()` fails.  I do not believe this is occuring  
under Linux.  
  
While testing, I tried using condition wait without the timeout, and  
it behaves fine. Looking closer, it appears that  
`scheduler::schedule(ctx)` actually checks sleep_is_linked() and  
unlinks when true.  
  
Am I using conditions incorrectly, or is  
`scheduler::schedule_from_remote` doing the wrong thing?  Maybe it  
should be checking sleep_is_linked, and unlinking also, being careful  
to protect against multithreaded access?  
  
  
```  
#include <boost/fiber/condition_variable.hpp>  
#include <boost/fiber/fiber.hpp>  
#include <boost/fiber/mutex.hpp>  
#include <boost/thread.hpp>  
  
boost::fibers::mutex g_mutex;  
boost::fibers::condition_variable g_condition;  
  
void thread1()  
{  
  Sleep(1000); // Wait for the other thread to get going.  
  printf("about to signal\n");  
  g_condition.notify_one();  
}  
  
int main()  
{  
  boost::thread t(thread1);  
  
  std::unique_lock<boost::fibers::mutex> lk(g_mutex);  
  printf("about to wait\n");  
  g_condition.wait_for(lk, std::chrono::seconds(5));  
  printf("done\n");  
  
  return 0;  
}  
```

---

## Comment 1

> Username: laqrix  
> Created at: 2017-05-17 20:41:05 UTC  
> Url: https://github.com/boostorg/fiber/issues/123#issuecomment-302225293  

I attempted to hack the assertion from `scheduler::schedule_from_remote` and including the following test after the final assert (which is after the spinlock_lock).  
```  
    if ( ctx->sleep_is_linked() ) {  
        // unlink it from sleep-queue  
        ctx->sleep_unlink();  
    }   
```  
I believe that when I'm using condition_variable::wait_for, my context will be on the sleep queue.  This appears to be symmetric with `scheduler::schedule`.  I feel like this is exactly the case of one thread modifying another thread's context that the documentation warns against.  Is the spinlock_lock strong enough to protect me here?

---

## Comment 2

> Username: olk  
> Created at: 2017-05-26 16:20:15 UTC  
> Url: https://github.com/boostorg/fiber/issues/123#issuecomment-304325486  

there was an unnecessary assertion (as you mentioned in the first comment) in scheduler::schedule_from_remote() - fixed now.
