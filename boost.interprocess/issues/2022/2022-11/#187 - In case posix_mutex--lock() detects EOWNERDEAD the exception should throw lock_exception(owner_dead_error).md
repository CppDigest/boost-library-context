# #187 - In case posix_mutex::lock() detects EOWNERDEAD the exception should throw lock_exception(owner_dead_error) [Closed]

> Username: wechmarer  
> Created at: 2022-11-13 15:56:52 UTC  
> Updated at: 2024-10-08 08:42:42 UTC  
> Closed at: 2024-10-03 09:31:19 UTC  
> Url: https://github.com/boostorg/interprocess/issues/187  

In boost/interprocess/sync/posix/mutex.hpp, line 105ff the error handling depends on return value of  `pthread_mutex_lock(&m_mut)`.  But regardless of whether the define BOOST_INTERPROCESS_POSIX_ROBUST_MUTEXES is set the `lock_exception()` throws the same reason for every case.  
Current: The thrown `error_code_t ` is always `not_recoverable `   
Expected: In case of EOWNERDEAD the thrown error code should be `owner_dead_error` to allow the application to react accordingly

---

## Comment 1

> Username: igaztanaga  
> Created at: 2022-11-17 15:26:24 UTC  
> Url: https://github.com/boostorg/interprocess/issues/187#issuecomment-1318801497  

If  `owner_dead_error` is thrown, the mutex remains locked unlike any other case that throws, which breaks mutex' class invariants. EOWNERDEAD does not match with C++ mutex semantics.

---

## Comment 2

> Username: wechmarer  
> Created at: 2022-11-18 11:36:27 UTC  
> Updated at: 2022-11-18 13:05:24 UTC  
> Url: https://github.com/boostorg/interprocess/issues/187#issuecomment-1319885101  

The exception shall still be thrown after unlock of mutex. I wrote a sample using the changed code and the behaviour is as expected.  
In case the other process died and did not unlock the mutex the return value of  `pthread_mutex_trylock ` is:  
- at first call: `EOWNERDEAD`  
- any further call: `ENOTRECOVERABLE`  
  
Therefor the lock_exception should return the expected values.  
  
Patch:  
```  
--- boost/interprocess/sync/posix/mutex.hpp.old 2022-11-18 12:29:14.884655008 +0100  
+++ boost/interprocess/sync/posix/mutex.hpp     2022-11-18 12:32:07.024655318 +0100  
@@ -109,7 +109,7 @@  
       //corrupted, so be safe and mark the mutex as not recoverable  
       //so applications can act accordingly.  
       pthread_mutex_unlock(&m_mut);  
-      throw lock_exception(not_recoverable);  
+      throw lock_exception(owner_dead_error);  
    }  
    else if (res == ENOTRECOVERABLE)  
       throw lock_exception(not_recoverable);  
@@ -128,7 +128,7 @@  
       //corrupted, so be safe and mark the mutex as not recoverable  
       //so applications can act accordingly.  
       pthread_mutex_unlock(&m_mut);  
-      throw lock_exception(not_recoverable);  
+      throw lock_exception(owner_dead_error);  
    }  
    else if (res == ENOTRECOVERABLE)  
       throw lock_exception(not_recoverable);  
@@ -156,7 +156,7 @@  
       //corrupted, so be safe and mark the mutex as not recoverable  
       //so applications can act accordingly.  
       pthread_mutex_unlock(&m_mut);  
-      throw lock_exception(not_recoverable);  
+      throw lock_exception(owner_dead_error);  
    }  
    else if (res == ENOTRECOVERABLE)  
       throw lock_exception(not_recoverable);  
```

---

## Comment 3

> Username: igaztanaga  
> Created at: 2024-10-03 09:31:19 UTC  
> Url: https://github.com/boostorg/interprocess/issues/187#issuecomment-2390949855  

Closing statle bugs.

---

## Comment 4

> Username: wechmarer  
> Created at: 2024-10-08 07:08:11 UTC  
> Updated at: 2024-10-08 07:08:44 UTC  
> Url: https://github.com/boostorg/interprocess/issues/187#issuecomment-2399020582  

I don't get it why the solution of the bug was rejected even though a patch was provided. However in the meantime we skipped the usage of boost for IPC due to this bug.

---

## Comment 5

> Username: igaztanaga  
> Created at: 2024-10-08 08:42:41 UTC  
> Url: https://github.com/boostorg/interprocess/issues/187#issuecomment-2399229394  

It is not a bug.  
  
EOWNERDEAD means you can do something to recover the mutex (pthread_mutex_consistent), but that's not true in the proposed patch. The real state of the mutex is [ENOTRECOVERABLE] because the state protected by the mutex is not recoverable.
