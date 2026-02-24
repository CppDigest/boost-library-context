# #235 - file_lock can't be used with scoped_lock because file_lock::internal_mutex_type is not defined [Open]

> Username: Tails86  
> Created at: 2024-10-15 15:17:36 UTC  
> Updated at: 2024-10-15 15:27:04 UTC  
> Url: https://github.com/boostorg/interprocess/issues/235  

For Boost version: 1.86.0  
  
The documentation [here](https://live.boost.org/doc/libs/1_85_0/doc/html/boost/interprocess/file_lock.html) states:  
"A file lock, is a mutual exclusion utility similar to a mutex using a file. A file lock has sharable and exclusive locking capabilities and can be used with [scoped_lock](https://live.boost.org/doc/libs/1_85_0/doc/html/boost/interprocess/scoped_lock.html) and [sharable_lock](https://live.boost.org/doc/libs/1_85_0/doc/html/boost/interprocess/sharable_lock.html) classes."  
  
When I try to use a `scoped_lock` with a `file_lock` like so:  
```  
boost::interprocess::file_lock* flk = nullptr; // Just for compilation testing purposes  
boost::interprocess::scoped_lock<boost::interprocess::file_lock> lock(*flk);  
```  
  
I run into the following compile error:  
```  
include/boost/interprocess/sync/detail/locks.hpp:35:60: error: no type named ‘internal_mutex_type’ in ‘boost::interprocess::scoped_lock<boost::interprocess::file_lock>::mutex_type’ {aka ‘class boost::interprocess::file_lock’}  
   35 |    typedef typename Lock::mutex_type::internal_mutex_type  mutex_type;  
```
