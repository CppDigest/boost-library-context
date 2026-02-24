# #373 - boost::thread_v2::upgrade_mutex corrupted when thread is interrupted [Open]

> Username: daniel-boehme  
> Created at: 2022-06-20 11:29:30 UTC  
> Updated at: 2022-06-20 11:29:30 UTC  
> Url: https://github.com/boostorg/thread/issues/373  

When a thread tries to obtain an exclusive lock on a `boost::thread_v2::upgrade_mutex` and is interrupted while waiting for all readers to finish, the mutex is left in a locked state. In turn, no other thread is able to lock the mutex again.  
The "owning" thread is unaware of this, as the function `lock()` returned with an exception.  
  
While interruptions are one way of leading to this problem, it might actually be a more general issue of exception safety.  
  
While this mutex implementation seems to be not used much, it is the workaround for the [writer starvation issue](https://github.com/boostorg/thread/issues/361) of the default mutex implementation on Linux.  
  
It seems like the default mutex implementation disables interruptions while trying to aquire the lock. I don't know if that is enough or if other exceptions might occur as well.  
  
Example code how to reproduce:  
```  
#define BOOST_THREAD_V2_SHARED_MUTEX  
#include <boost/thread.hpp>  
  
using mutex_t = boost::shared_mutex;  
using shared_lock_t = boost::shared_lock<boost::shared_mutex>;  
using unique_lock_t = boost::unique_lock<boost::shared_mutex>;  
  
int main(int, char**) {  
    mutex_t mut;  
    boost::barrier readerReady(2), readerDone(2);  
      
    auto t1 = boost::thread([&]() {  
        shared_lock_t guard(mut);  
        readerReady.wait();  
        readerDone.wait();  
    });  
      
    readerReady.wait();  
      
    auto t2 = boost::thread([&]() {  
        unique_lock_t guard(mut);  
    });  
      
    boost::this_thread::sleep_for(boost::chrono::seconds(2));  
      
    t2.interrupt();  
    t2.join();  
      
    readerDone.wait();  
    t1.join();  
      
    BOOST_ASSERT(mut.try_lock());  
      
    return 0;  
}  
```  
Please note that the example does not terminate when `BOOST_THREAD_V2_SHARED_MUTEX` is not defined. It then hangs in `t2.join()`, as `t2` has disabled interruptions when acquiring a lock.
