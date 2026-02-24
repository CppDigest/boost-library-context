# #274 - blocked in message_queue::timed_receive [Open]

> Username: liuaifu  
> Created at: 2025-10-23 13:30:23 UTC  
> Updated at: 2026-01-04 23:34:41 UTC  
> Url: https://github.com/boostorg/interprocess/issues/274  

I'm using boost_1_89_0 & boost::interprocess::message_queue for inter-process communication. The receiver calls timed_receive to receive messages with a timeout set to 100 milliseconds. It works correctly most of the time, but occasionally it gets blocked, meaning timed_receive does not return.  
  
```  
boost::interprocess::message_queue *msg_queue;  
// ...  
boost::posix_time::ptime timeout = boost::posix_time::microsec_clock::universal_time() + boost::posix_time::milliseconds(100);  
bool ret = msg_queue->timed_receive(buf, 8192, recvd_size, priority, timeout);  
```  
  
I checked the call stack, and it is blocked at the following location:  
  
boost\interprocess\sync\detail\common_algorithms.hpp  
  
```  
template<class MutexType>  
void try_based_lock(MutexType &m)  
{boost::interprocess::message_queue  
   if(!m.try_lock()){  
      spin_wait swait;  
      do{  
         if(m.try_lock()){  
            break;  
         }  
         else{  
            swait.yield();  
         }  
      }  
      while(1);  
   }  
}  
```  
  
I stepped through the code and found that `m.try_lock()` keeps returning false.  
  
Thanks.

---

## Comment 1

> Username: igaztanaga  
> Created at: 2026-01-04 23:34:41 UTC  
> Url: https://github.com/boostorg/interprocess/issues/274#issuecomment-3708531165  

I've tried to analyze your problem, but I have no enough context to analyze it. Could provide please the platform/compiler and the call stack to know where is blocked?
