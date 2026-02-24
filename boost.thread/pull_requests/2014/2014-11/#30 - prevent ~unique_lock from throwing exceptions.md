# #30 prevent ~unique_lock from throwing exceptions [Closed]

> Username: tempoz  
> Created at: 2014-11-14 20:56:03 UTC  
> Updated at: 2015-03-16 14:52:15 UTC  
> Closed at: 2014-12-06 09:54:06 UTC  
> Url: https://github.com/boostorg/thread/pull/30  

~unique_lock() calls unlock(), which can throw an exception. Destructors may not throw exceptions on pain of immediately terminating the program, so we have to catch the exception.

---

## Comment 1

> Username: TyRoXx  
> Created_at: 2014-11-14 21:56:58 UTC  
> Url: https://github.com/boostorg/thread/pull/30#issuecomment-63135443  

Isn't lock_error always a symptom of a bug? Why hide that?

---

## Comment 2

> Username: viboes  
> Created_at: 2014-11-15 17:38:20 UTC  
> Url: https://github.com/boostorg/thread/pull/30#issuecomment-63180724  

In which cases could unlock() throw?

---

## Comment 3

> Username: viboes  
> Created_at: 2014-12-06 09:54:06 UTC  
> Url: https://github.com/boostorg/thread/pull/30#issuecomment-65891618  

I think this PR is invalid.

---

## Comment 4

> Username: tempoz  
> Created_at: 2015-03-13 20:08:18 UTC  
> Url: https://github.com/boostorg/thread/pull/30#issuecomment-79326595  

Here is the function body of `unlock()`:  
  
``` cpp  
void unlock()  
{  
    int res = posix::pthread_mutex_unlock(&m);  
    if (res)  
    {  
        boost::throw_exception(lock_error(res,"boost: mutex unlock failed in pthread_mutex_unlock"));  
    }  
}  
```  
  
So `unlock()` throws when `posix::pthread_mutex_unlock` returns any non-zero value. It is not my goal to hide this by catching the exception, just to prevent the entire program from violently terminating. If someone wanted to add logging in the catch block, I would not be opposed.

---

## Comment 5

> Username: viboes  
> Created_at: 2015-03-14 07:30:03 UTC  
> Url: https://github.com/boostorg/thread/pull/30#issuecomment-80071487  

There is no valid reason a call to `mutex::unlock()` inside a `unique_lock` throws.  
  
```  
  
  
    The pthread_mutex_lock() and pthread_mutex_trylock() functions shall fail if:  
  
    [EINVAL]  
        The mutex was created with the protocol attribute having the value PTHREAD_PRIO_PROTECT and the calling thread's priority is higher than the mutex's current priority ceiling.  
  
    The pthread_mutex_trylock() function shall fail if:  
  
    [EBUSY]  
        The mutex could not be acquired because it was already locked.  
  
    The pthread_mutex_lock(), pthread_mutex_trylock(), and pthread_mutex_unlock() functions may fail if:  
  
    [EINVAL]  
        The value specified by mutex does not refer to an initialized mutex object.  
    [EAGAIN]  
        [XSI] [Option Start] The mutex could not be acquired because the maximum number of recursive locks for mutex has been exceeded. [Option End]  
  
    The pthread_mutex_lock() function may fail if:  
  
    [EDEADLK]  
        A deadlock condition was detected or the current thread already owns the mutex.  
  
    The pthread_mutex_unlock() function may fail if:  
  
    [EPERM]  
        The current thread does not own the mutex.  
  
    These functions shall not return an error code of [EINTR].  
```

---

## Comment 6

> Username: viboes  
> Created_at: 2015-03-14 07:36:09 UTC  
> Url: https://github.com/boostorg/thread/pull/30#issuecomment-80079129  

What I could/should do is to don't throw on `Mutex::unlock()` . From the documentation  
  
```  
m.unlock();  
  
Requires:  
  
    The current thread owns m.   
Synchronization:  
  
    This operation synchronizes with subsequent lock operations that obtain ownership on the same object.   
Effects:  
  
    Releases a lock on m by the current thread.   
Return type:  
  
    void.   
Throws:  
  
    Nothing.   
```  
  
The standard requires the same. I will create a Trac ticket.

---

## Comment 7

> Username: tempoz  
> Created_at: 2015-03-16 14:52:15 UTC  
> Url: https://github.com/boostorg/thread/pull/30#issuecomment-81711053  

Thank you; this solves my problem as well.

---
