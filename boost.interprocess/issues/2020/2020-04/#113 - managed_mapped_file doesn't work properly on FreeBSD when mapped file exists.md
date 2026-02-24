# #113 - managed_mapped_file doesn't work properly on FreeBSD when mapped file exists [Closed]

> Username: tinlans  
> Created at: 2020-04-15 03:38:16 UTC  
> Updated at: 2020-06-03 02:13:23 UTC  
> Closed at: 2020-06-03 02:13:23 UTC  
> Url: https://github.com/boostorg/interprocess/issues/113  

boost version: 1.72.0  
OS: FreeBSD 12.1-STABLE  
Compiler: Clang 9.0.1  
  
To reproduce this issue, you could run the following code twice:  
```  
using MmapAllocator = boost::interprocess::allocator<int32_t, managed_mapped_file::segment_manager>;  
using MmapVector = std::vector<int32_t, MmapAllocator>;  
  
managed_mapped_file file(open_or_create, "file", 65536);  
MmapAllocator mmapAllocator(file.get_segment_manager());  
MmapVector *vec = file.find_or_construct<MmapVector>("MMapVector1")(mmapAllocator);  
vec->push_back(1);  
```  
And you will receive the lock_exception thrown by the function posix_mutex::lock(), because the result of the function call `pthread_mutex_lock(&m_mut)` is EINVAL.  
  
I found that the main issue is the implementation of the type pthread_mutex_t. It's a union type on Linux, and I guess its state can be serialized to the disk and restored from the disk. On the other hands, the type of pthread_mutex_t is `struct pthread_mutex *` on FreeBSD, and it's obviously that the pointer cannot be restored properly.  
  
When the mapped file exists, the construction flow of managed_mapped_file will reach `boost::interprocess::ipcdetail::basic_managed_memory_impl::open_impl()` and try to restore its member `mp_header`. The pthread_mutex_t instance I mentioned above is a part of `mp_header`, so it results in an invalid pointer of the type `struct pthread_mutex *` on FreeBSD. Therefore, when the allocator of the segment manager tries to allocate more bytes, it tries to lock the mutex and pthread_mutex_lock() tells you it's an invalid one.

---

## Comment 1

> Username: igaztanaga  
> Created at: 2020-06-02 12:32:14 UTC  
> Url: https://github.com/boostorg/interprocess/issues/113#issuecomment-637511296  

The main goal of process-shared mutexes (PTHREAD_PROCESS_SHARED) is to be able to map the mutex in different base addresses. So if FreeBSD declares that supports PTHREAD_PROCESS_SHARED then it can't be implemented as pointers.  
  
Is _POSIX_THREAD_PROCESS_SHARED declared in FreeBSD 12.0 but not correctly implemented?

---

## Comment 2

> Username: tinlans  
> Created at: 2020-06-03 02:13:23 UTC  
> Url: https://github.com/boostorg/interprocess/issues/113#issuecomment-637911372  

>   
>   
> The main goal of process-shared mutexes (PTHREAD_PROCESS_SHARED) is to be able to map the mutex in different base addresses. So if FreeBSD declares that supports PTHREAD_PROCESS_SHARED then it can't be implemented as pointers.  
>   
> Is _POSIX_THREAD_PROCESS_SHARED declared in FreeBSD 12.0 but not correctly implemented?  
  
Thanks for the hint.  
  
FreeBSD 12 indeed declared the macro _POSIX_THREAD_PROCESS_SHARED; however, I found the fact that the default behavior is different from the one on Linux. After reading the man page of libthr(3), I saw the description of the sysctl variable kern.ipc.umtx_vnode_persistent:  
> kern.ipc.umtx_vnode_persistent: By default, a shared lock backed by a mapped file in memory is automatically destroyed on the last unmap of the corresponding file's page, which is allowed by POSIX. Setting the sysctl to 1 makes such a shared lock object persist until the vnode is recycled by the Virtual File System.  Note that in case file is not opened and not mapped, the kernel might recycle it at any moment, making this sysctl less useful than it sounds.  
  
Process-shared mutexes work like the ones on Linux after I set this sysctl variable to 1, although I'm a little worried about the last sentence of the description regarding to the recycling mechanism.
