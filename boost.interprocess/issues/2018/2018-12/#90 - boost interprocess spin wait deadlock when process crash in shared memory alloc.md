# #90 - boost interprocess spin wait  deadlock  when process crash in shared memory alloc [Closed]

> Username: 363568233  
> Created at: 2018-12-04 10:23:06 UTC  
> Updated at: 2026-01-07 09:46:13 UTC  
> Closed at: 2026-01-07 09:46:13 UTC  
> Url: https://github.com/boostorg/interprocess/issues/90  

void yield()  
   {  
      //Lazy initialization of limits  
      if( !m_k){  
         this->init_limits();  
      }  
      //Nop tries  
      if( m_k < (nop_pause_limit >> 2) ){  
  
      }  
      //Pause tries if the processor supports it  
      #if defined(BOOST_INTERPROCESS_SMT_PAUSE)  
      else if( m_k < nop_pause_limit ){  
         BOOST_INTERPROCESS_SMT_PAUSE  
      }  
      #endif  
      //Yield/Sleep strategy  
      else{  
         //Lazy initialization of tick information  
         if(m_k == nop_pause_limit){  
            this->init_tick_info();  
         }  
         else if( this->yield_or_sleep() ){  
            ipcdetail::thread_yield();  
         }  
         else{  
            ipcdetail::thread_sleep_tick();  
         }  
      }  
      ++m_k;  
   }

---

## Comment 1

> Username: rezaebrh  
> Created at: 2019-05-29 07:11:53 UTC  
> Updated at: 2019-06-09 14:48:46 UTC  
> Url: https://github.com/boostorg/interprocess/issues/90#issuecomment-496814342  

I used `try_atomic_func()` which gets segment_manager as its parameter. The function will return false in case of segment is locked. BUT concurrency issues might happen in some cases (e.g when a thread is allocating while another thread is trying to find the allocated memory.  
  
**Edited: BUT I'm looking forward to find a better solution

---

## Comment 2

> Username: igaztanaga  
> Created at: 2026-01-07 09:46:13 UTC  
> Url: https://github.com/boostorg/interprocess/issues/90#issuecomment-3718065405  

Closing stale bugs, sorry for the inconvenience.
