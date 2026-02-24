# #151 - Feature Request: Provide non-throwing methods to open/create shared_memory_object [Open]

> Username: Lastique  
> Created at: 2021-09-15 11:30:15 UTC  
> Updated at: 2021-09-15 11:30:15 UTC  
> Url: https://github.com/boostorg/interprocess/issues/151  

My use case is as follows. I need to synchronize multiple processes or threads initializing a shared memory region. No select process can be considered the "master", so it is not known which of the processes will actually create and initialize the shared memory object. The natural solution would be to construct `shared_memory_object(open_or_create)`, but this introduces a problem that the caller doesn't know whether the shared memory was created or an existing region was opened. This is important to later decide whether the caller should resize the region to the desired size and initialize the contents or try and adopt the existing one. (Testing the region size for 0 is not safe because it introduces a race condition.)  
  
The current API does not provide a way to implement this efficiently. It is possible to implement a loop trying to construct `shared_memory_object(open_only)` or `shared_memory_object(create_only)`, but the constructors throw on failure, which is unnecessary overhead and awkward to use in the caller. Indeed, you have implemented exactly that [here](https://github.com/boostorg/interprocess/blob/e4259fe16804f5a4b179e1c7e69d201bd532d31a/include/boost/interprocess/detail/managed_open_or_create_impl.hpp#L285-L338). It would be better to have a set of methods in `shared_memory_object` that allow to create or open the shared memory and able to report the result without throwing - for example, through an `error_code` argument.  
  
Also, please consider adding a way to create a shared memory segment of a given size. On POSIX, this would allow the aforementioned loop to be implemented in Boost.Interprocess instead of user's code. On Windows, `CreateFileMappingW` already accepts the desired size. This new API would also have to provide a result so that the caller knows whether a new segment was created or not.
