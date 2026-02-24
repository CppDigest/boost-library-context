# #65 - robustness of the interprocess mutex [Closed]

> Username: reed-lau  
> Created at: 2018-10-26 05:27:39 UTC  
> Updated at: 2021-08-21 22:31:31 UTC  
> Closed at: 2021-08-21 22:31:31 UTC  
> Url: https://github.com/boostorg/interprocess/issues/65  

when using interprocess shared memroy, there are about two locks. one is the internal mutex_family used by boost for managing the sharedmemory in MemoryAlgorithm, one is the user's one for synchronation. both  mutex will cause deadlock, when process crash. How do you think about it.  
  
the linux provide pthread_mutexattr_setrobust, but the interprocess module do not provide the interface, eg. interprocess:scoped_lock's lock's return value is void instead of int, which could be compare with EOWNERDEAD.
