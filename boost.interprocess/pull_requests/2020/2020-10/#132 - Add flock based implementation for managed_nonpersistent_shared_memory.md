# #132 Add flock based implementation for managed_nonpersistent_shared_memory [Open]

> Username: stheophil  
> Created at: 2020-10-09 12:08:15 UTC  
> Updated at: 2024-09-26 21:55:49 UTC  
> Url: https://github.com/boostorg/interprocess/pull/132  

Hi Ion,  
  
a few years back we ran into the same problem like many other boost.interprocess users. Posix shared memory / file-backed shared memory semantics make it hard to recover after one of the participating processes has terminated unexpectedly, see   
[[1]](https://boost-users.boost.narkive.com/ELcBubNw/interprocess-semaphore-cleanup-after-crash) [[2]](https://groups.google.com/g/boost-list/c/EnCUfnmPFHQ) [[3]](http://boost.2283326.n4.nabble.com/interprocess-shared-memory-lifetime-td2603982.html) [[4]](https://stackoverflow.com/questions/37756328/how-to-free-managed-shared-memory-after-a-program-crash-what-are-effective-tech) and issues https://github.com/boostorg/interprocess/issues/65 https://github.com/boostorg/interprocess/issues/56 https://github.com/boostorg/interprocess/issues/109  
  
Windows shared memory fixes this by releasing the shared memory object. Linux has robust locks that tell the user that the owner has crashes. Other systems such as macOS have no easy solution.  
  
We have used and tested the attached solution for the last few years on macOS and hope that it is robust. The core of the implementation is `nonpersistent_shared_memory_object::priv_open_or_create`   
  
In short, the backing file is first opened with an exclusive lock, which indicates that the caller is the first to open the file. The file is then truncated and the lock is degraded to a shared lock. When obtaining the exclusive lock fails, the caller tries immediately to get a shared lock.  
  
A callback notifies the user if the shared memory was created, not opened and lets the user reinitialize associated objects such as semaphores. See the example in `example/cpp11_nonpersistent_shared_memory.cpp`  
  
There are frequently documented problems with flock that don't seem to be a problem here:  
  
- Switching from an exclusive lock to a shared lock is not actually atomic. Only two things can happen (see comments in `nonpersistent_shared_memory_object::priv_open_or_create`:  
    1) After the exclusive lock is released but before the degraded shared lock is acquired another waiting process acquires the shared lock. The order in which processes with shared locks proceed does not matter.  
    2) After the exclusive lock is released another process acquires the exclusive lock and truncates the file to size 0 again, then both acquire shared locks and proceed. Also seems harmless.  
  
- flock style locks do not work reliably on NFS file systems. Using shared memory backing files on different computers simultaneously seems to be an accident and not a feature. If the backing file is stored on an NFS drive but only accessed locally (by adding a host identifier to the file name?), NFS `locallocks` seems to be well supported and sufficient.

---
