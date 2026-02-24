# #74 - named_mutex  posix should change semaphore to robust pthread_mutex [Closed]

> Username: 363568233  
> Created at: 2018-12-17 10:34:52 UTC  
> Updated at: 2021-08-21 22:46:18 UTC  
> Closed at: 2021-08-21 22:46:18 UTC  
> Url: https://github.com/boostorg/interprocess/issues/74  

current default implementation  use BOOST_INTERPROCESS_FORCE_GENERIC_EMULATION macro,  
#include <boost/interprocess/sync/shm/named_mutex.hpp> may deadlock when ower dead  
#include <boost/interprocess/sync/posix/named_mutex.hpp> is  the posix semaphore, it may also deadlock when ower dead.  
why not change the named_mutx.hpp to mutex.hpp? using robust pthread_mutex  
I see interprocess_mutex have been robust

---

## Comment 1

> Username: igaztanaga  
> Created at: 2021-08-21 22:46:18 UTC  
> Url: https://github.com/boostorg/interprocess/issues/74#issuecomment-903184972  

pthread mutexes are anonymous resources, not named ones. Recent commits have activated robust mutexes in interprocess_mutex and interprocess_recursive_mutex in platforms that support it. Using named semaphores for named mutexes is also used in other runtimes like APR (Apache Portable Runtime) so no plans to change this in Interprocess.
