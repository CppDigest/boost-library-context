# #142 - File lock documentation about advisory locking is inaccurate on Windows [Closed]

> Username: sly1061101  
> Created at: 2021-04-15 16:17:15 UTC  
> Updated at: 2021-08-23 08:15:01 UTC  
> Closed at: 2021-08-23 08:14:53 UTC  
> Url: https://github.com/boostorg/interprocess/issues/142  

The file lock documentation explicitly mentions:  
  
> Advisory locking: The operating system kernel maintains a list of files that have been locked. But does not prevent writing to those files even if a process has acquired a sharable lock or does not prevent reading from the file when a process has acquired the exclusive lock. Any process can ignore an advisory lock. This means that advisory locks are for cooperating processes, processes that can trust each other. This is similar to a mutex protecting data in a shared memory segment: any process connected to that memory can overwrite the data but cooperative processes use mutexes to protect the data first acquiring the mutex lock.  
  
> Boost.Interprocess implements advisory blocking because of portability reasons. This means that every process accessing to a file concurrently, must cooperate using file locks to synchronize the access.  
  
But on Windows, `boost::interprocess::file_lock` doesn't seems to be advisory. As mentioned in another issue https://github.com/boostorg/interprocess/issues/98, once `boost::interprocess::file_lock` locks a file on Windows, no other process can access it, and even the same process cannot access it with a different handler.

---

## Comment 1

> Username: igaztanaga  
> Created at: 2021-08-23 08:15:01 UTC  
> Url: https://github.com/boostorg/interprocess/issues/142#issuecomment-903544755  

Many thanks for the report!
