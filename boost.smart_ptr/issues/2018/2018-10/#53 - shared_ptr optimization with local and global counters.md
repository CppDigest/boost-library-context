# #53 - shared_ptr optimization with local and global counters [Open]

> Username: breese  
> Created at: 2018-10-07 11:49:23 UTC  
> Updated at: 2018-10-07 11:49:23 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/53  

Would it be desirable to have a `shared_ptr` (or extend the existing `shared_ptr`) with a thread-local reference counter?  
  
The basic idea is to have two reference counters: the normal reference counter is changed to count the number of threads using the `shared_ptr`, and an additional thread-local reference counter is used when a thread copies/deletes the `shared_ptr` (like `local_shared_ptr`.)  
  
This ought to yield better performance in multi-threaded applications as it reduces the amount of cache congestion on the normal reference counter.  
  
The disadvantages is the increased size due to the extra counter, and we need to consider how to pass `shared_ptr` between threads.
