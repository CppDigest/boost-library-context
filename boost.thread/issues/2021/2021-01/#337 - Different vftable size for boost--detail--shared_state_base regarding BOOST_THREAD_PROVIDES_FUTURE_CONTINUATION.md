# #337 - Different vftable size for boost::detail::shared_state_base regarding BOOST_THREAD_PROVIDES_FUTURE_CONTINUATION [Open]

> Username: yvoronov-artec  
> Created at: 2021-01-22 14:09:52 UTC  
> Updated at: 2021-01-22 14:11:15 UTC  
> Url: https://github.com/boostorg/thread/issues/337  

**Version of Boost**: All of them, including 'master'  
  
**File**:  `thread/include/boost/thread/future.hpp `, around line 327  
  
The matter is that this file contains the following portion of code (class boost::detail::shared_state_base):  
```  
#if defined BOOST_THREAD_PROVIDES_FUTURE_CONTINUATION  
            virtual void set_continuation_ptr(continuation_ptr_type continuation, boost::unique_lock<boost::mutex>& lock)  
            {  
              continuations.push_back(continuation);  
              if (done) {  
                do_continuation(lock);  
              }  
            }  
#endif  
```  
So the `vftable` layout depends on BOOST_THREAD_PROVIDES_FUTURE_CONTINUATION macro, which leads to linker warning (and unpredictable runtime outcomes) when mixing code from two different modules, with only one of them using that macro.  
  
There are some precautions about this issue before (see ll.285 and 304). It would be nice to have the same here.
