# #268 - unbuffered_channel push hangs [Closed]

> Username: BenKaufmann  
> Created at: 2021-01-19 12:14:52 UTC  
> Updated at: 2021-01-19 15:18:41 UTC  
> Closed at: 2021-01-19 15:18:41 UTC  
> Url: https://github.com/boostorg/fiber/issues/268  

Starting with boost-version 1.75.0 (more precisely since the merge of https://github.com/boostorg/fiber/pull/261), the following simple example hangs because fiber p1 never returns from the `push(12)` call:  
```cpp  
#include <boost/fiber/operations.hpp>                                             
#include <boost/fiber/condition_variable.hpp>                                     
#include <boost/fiber/unbuffered_channel.hpp>                                     
                                                                                  
#include <stdio.h>                                                                
                                                                                  
int main()                                                                        
{                                                                                 
  boost::fibers::unbuffered_channel<int> channel;                                 
  boost::fibers::condition_variable      cond;                                    
  boost::fibers::mutex                   mutex;                                   
  boost::fibers::fiber                   consumer(boost::fibers::launch::dispatch, [&]() {  
    for (int v; channel.pop(v) == boost::fibers::channel_op_status::success; )    
    {                                                                             
      boost::this_fiber::yield();                                                 
      cond.notify_one();                                                          
    }                                                                             
  });                                                                             
  boost::fibers::fiber                   p1(boost::fibers::launch::post, [&]() {  
    printf("PF: push(12)\n");                                                     
    channel.push(12);                                                             
    printf("PF: done\n");                                                         
  });                                                                             
  printf("MF: push(22)\n");                                                       
  channel.push(22);                                                               
  printf("MF: wait\n");                                                           
  std::unique_lock lock(mutex);                                                   
  cond.wait(lock);                                                                
  printf("MF: join PF\n");                                                        
  p1.join();                                                                      
  channel.close();                                                                
  consumer.join();                                                                
}  
```  
The problem seems to be due to an outdated waker in `unbuffered_channel::push()`:  
* Once fiber p1 enters  `unbuffered_channel::push(12)`, it immediately creates a slot and waker with epoch E1.  
* The call to `try_push()` then returns false because the consumer has not yet popped the 22  
* Hence, `waiting_producers_.suspend_and_wait( lk, active_ctx);` is called, which creates another waker with epoch E2  
* The consumer fiber now pops the first value thereby scheduling p1 from the `waiting_producers_`. It then `boost::this_fiber::yield()`s back to p1.  
* Fiber p1 now again calls `try_push()`, which succeeds and p1 is suspended until the slot is popped. **However**, the waker that is stored in the pushed slot is already outdated at this point!  
* Eventually, the consumer pops the slot that was pushed by p1 and calls `s->w.wake();` on it. **However**, given that the waker is outdated, p1 is never scheduled and hangs forever.  
  
As far as I can see, one could fix the issue by delaying the creation of the waker that is associated with a pushed slot.  
I.e. one would create the slot without a waker and instead call `s.w = active_ctx->create_waker();` only right before the call to `try_push()`.

---

## Comment 1

> Username: olk  
> Created at: 2021-01-19 15:18:41 UTC  
> Url: https://github.com/boostorg/fiber/issues/268#issuecomment-762910087  

ty
