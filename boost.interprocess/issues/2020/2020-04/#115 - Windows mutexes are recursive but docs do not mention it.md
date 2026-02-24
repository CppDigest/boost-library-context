# #115 - Windows mutexes are recursive but docs do not mention it [Closed]

> Username: WSudul  
> Created at: 2020-04-30 19:28:22 UTC  
> Updated at: 2021-02-18 23:33:26 UTC  
> Closed at: 2021-02-18 23:33:26 UTC  
> Url: https://github.com/boostorg/interprocess/issues/115  

Hello,  
  
**It seems that boost interprocess mutex on Windows is really a recursive mutex.**  
This seems to be intentional, since winapi specifices that CreateMutex() calls create mutex object that are reentrant.   
  
**I couldnt find anything mentioned in documentation about it**, I had to dwelve into source code and check that `winapi::open_or_create_mutex` and `winapi_wrapper_timed_wait_for_single_object` are thin wrappers on winapi calls.   
  
The only mention of this "feature" is in [recursive_mutex.hpp](https://github.com/boostorg/interprocess/blob/master/include/boost/interprocess/sync/windows/recursive_mutex.hpp)  
```cpp  
//Windows mutex is already recursive  
class windows_recursive_mutex  
   : public windows_mutex  
```  
**Can we somehow indicate in docs that windows mutexes are recursive?**   
This would save time for debugging when code on windows does not deadlock/crash while posix does. For me it's obvious since I had already read MS's docs regarding synchronziations ,but for anyone else it might come as unwelcome surpsie  
**Can (or should) we rework current implementation?**   
I assume we could use winapi `DWORD GetCurrentThreadId();` which is supposedly unique for entire system.  
When we acquire mutex (via `lock()`), we could compare with some new member field `owner_thread_id`. If it's not same as current thread id, then we update the field and proceed as normal.  
 If it's same as current thread id, then we can throw exception/signal error. Later would clear the field in `unlock()`.  
**Of course this could introduce potential bug where we deadlock if thread_id reuse occurs**  
-thread (t1) owns mutex and dies without releasing it  
-same process starts new thread (t2) and OS reuses the thread id for t2   
-t2 is now throwing exceptions while trying to acquire mutex because internal state is conflicting  
Similiar scenario could be produced if we replace t1 and t2 with processes, since thread and process ids are taken from same pool.

---

## Comment 1

> Username: igaztanaga  
> Created at: 2021-02-18 22:26:00 UTC  
> Url: https://github.com/boostorg/interprocess/issues/115#issuecomment-781675234  

Whether a mutex is really recursive or not, it's not something that the user should rely on, as the implementation can change. Following the C++ standard locking a mutex twice is not a defined behaviour:  
  
_Requires: If m is of type mutex, timed_mutex, shared_mutex, or shared_timed_mutex, the calling  
thread does not own the mutex._  
  
_[Note: A program may deadlock if the thread that owns a mutex object calls lock() on that object. If the implementation can detect the deadlock, a resource_deadlock_would_occur error condition may be observed. —end note]_  
  
In any case, windows_recursive_mutex-like classes are implementation detail classes that should not documented as this implementation detail can change at any time. I'll add a note in lock() operations explaining the similar conditions for Interprocess mutexes.
