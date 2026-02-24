# #56 - boost::named_mutex remains acquired after crash [Closed]

> Username: skryv-softserveinc  
> Created at: 2018-05-08 11:21:35 UTC  
> Updated at: 2026-01-07 14:10:24 UTC  
> Closed at: 2026-01-07 14:10:24 UTC  
> Url: https://github.com/boostorg/interprocess/issues/56  

tried on macOS

---

## Comment 1

> Username: 363568233  
> Created at: 2018-12-17 06:30:08 UTC  
> Url: https://github.com/boostorg/interprocess/issues/56#issuecomment-447736671  

It exists on any OS. Because the default named_mutex is spin_wait, you can view the source code, there is macro to change internal implementation. You can change it to posix pthread_mutex on macOS, now it update to robust mutex, maybe you need to update the source code.

---

## Comment 2

> Username: 363568233  
> Created at: 2018-12-17 06:34:51 UTC  
> Url: https://github.com/boostorg/interprocess/issues/56#issuecomment-447737526  

Changed the posix mutex implementation to be robust #66

---

## Comment 3

> Username: 363568233  
> Created at: 2018-12-17 10:06:46 UTC  
> Url: https://github.com/boostorg/interprocess/issues/56#issuecomment-447789246  

#if defined(BOOST_INTERPROCESS_USE_POSIX_SEMAPHORES)  
      typedef ipcdetail::posix_named_mutex      internal_mutex_type;  
      #undef BOOST_INTERPROCESS_USE_POSIX_SEMAPHORES  
   #elif defined(BOOST_INTERPROCESS_USE_WINDOWS)  
      typedef ipcdetail::windows_named_mutex    internal_mutex_type;  
      #undef BOOST_INTERPROCESS_USE_WINDOWS  
   #else  
      typedef ipcdetail::shm_named_mutex        internal_mutex_type;  
   #endif  
But define BOOST_INTERPROCESS_USE_POSIX_SEMAPHORES, boost using the posix semaphore, deadlock are also possible.  You should use posix_mutex shared in process, but named_mutex dont't use posix_mutex.  I did it myself...

---

## Comment 4

> Username: seanlis  
> Created at: 2020-10-31 13:33:14 UTC  
> Url: https://github.com/boostorg/interprocess/issues/56#issuecomment-719934673  

Would this issue ever get fixed? It has been there for almost a decade.  
I spent one whole day to add named_mutex and named_condition to my windows apps, and then found out this issue forcing me to look for other solutions.

---

## Comment 5

> Username: Dalzhim  
> Created at: 2022-11-11 19:46:05 UTC  
> Url: https://github.com/boostorg/interprocess/issues/56#issuecomment-1312137601  

I'm also wondering why the PR #66 was closed without ever being merged. Running into this problem is quite annoying when a known solution has existed for so long!

---

## Comment 6

> Username: igaztanaga  
> Created at: 2026-01-07 14:10:24 UTC  
> Url: https://github.com/boostorg/interprocess/issues/56#issuecomment-3719066224  

In MacOS, there is no operating system support for robust mutexes in shared memory. In systems with support for robust mutexes in shared memory (e.g. linux), the library already uses this feature.
