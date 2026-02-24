# #106 Use fallocate on truncate_file [Closed]

> Username: xonatius  
> Created at: 2019-12-06 00:09:57 UTC  
> Updated at: 2022-08-19 12:00:29 UTC  
> Closed at: 2021-02-03 08:15:48 UTC  
> Url: https://github.com/boostorg/interprocess/pull/106  

On linux platform, boost interprocess shared memory uses shared memory files allocated in /dev/shm which is tmpfs. Upon creating a file [1], boost tried to resize it to a requested length [2] through a ftruncate call, which changes the size but does not actually guarantee that the memory will be allocated. As result, when accessing the memory linux kernel will try to allocate it and if the host is under memory pressure, the allocation attempt may fail and application will receive a SIGBUS and crash.  
  
This change introduces posix_fallocate call, which guarantees memory to be allocated or otherwise the call will fail. This behavior is easier to handle than recovering after SIGBUS. The fallocate is supported for tmpfs, which is used for /dev/shm since linux 3.5 [3]. For older versions, which do not support fallocate syscall, the glibc will emulate the behavior.  
  
[1] https://github.com/boostorg/interprocess/blob/boost-1.68.0/include/boost/interprocess/detail/managed_open_or_create_impl.hpp#L368  
  
[2] https://github.com/boostorg/interprocess/blob/boost-1.68.0/include/boost/interprocess/detail/managed_open_or_create_impl.hpp#L403  
  
[3] https://elixir.bootlin.com/linux/v3.5/source/mm/shmem.c#L2721

---

## Comment 1

> Username: roehling  
> Created_at: 2020-12-07 19:07:56 UTC  
> Url: https://github.com/boostorg/interprocess/pull/106#issuecomment-740119387  

This will probably fix #114

---

## Comment 2

> Username: igaztanaga  
> Created_at: 2021-02-03 08:16:44 UTC  
> Url: https://github.com/boostorg/interprocess/pull/106#issuecomment-772322556  

Many thanks! I've applied a patch based on your pull request.

---
