# #207 - boost::filesystem::copy_file: Function not implemented (1.76) [Closed]

> Username: basiliscos  
> Created at: 2021-09-28 09:09:27 UTC  
> Updated at: 2021-09-28 10:11:29 UTC  
> Closed at: 2021-09-28 10:11:29 UTC  
> Url: https://github.com/boostorg/filesystem/issues/207  

Hi.   
  
When I cross-compile boost for aarch64, and then run tests via `qemu-aarch64` inside docker, I got the error. When there is no `docker` between, there is no problem, i.e. the same binary executes perfectly. Unfortunately, we use CI for testing, hence, it would be nice to let `copy_file` work.   
  
As far as I was able to google, the issue is in `fallocate` syscall (aka 285) (see https://forums.percona.com/t/does-docker-use-unionfs-and-does-it-cause-fallocate-operation-not-supported/8161 )  
  
Here is an excerpt of runnign `qemu-aarch64 -strace -L /path/to/my/sysroot my-binary`  
  
```  
607 newfstatat(AT_FDCWD,"/home/conan/b8f3-d6a1-a97d-8301.defaults",0x00000055007ffc60,0) = 0  
file = /home/conan/b8f3-d6a1-a97d-8301.defaults  
607 write(1,0x4f99e0,48) = 48  
607 newfstatat(AT_FDCWD,"/home/conan/b8f3-d6a1-a97d-8301.defaults",0x00000055007ffaf0,0) = 0  
607 newfstatat(AT_FDCWD,"/home/conan/b8f3-d6a1-a97d-8301",0x00000055007ffaf0,0) = -1 errno=2 (No such file or directory)  
607 openat(AT_FDCWD,"/home/conan/b8f3-d6a1-a97d-8301.defaults",O_RDONLY|O_CLOEXEC) = 3  
607 fstat(3,0x00000055007ffa70) = 0  
607 openat(AT_FDCWD,"/home/conan/b8f3-d6a1-a97d-8301",O_WRONLY|O_CREAT|O_EXCL|O_TRUNC|O_CLOEXEC,S_IFREG|0644) = 4  
607 fstat(4,0x00000055007ffaf0) = 0  
607 Unknown syscall 285  
607 futex(0x0000005500bff220,FUTEX_PRIVATE_FLAG|FUTEX_WAKE,2147483647,NULL,0x00000055009d5000,10309632) = 0  
607 close(4) = 0  
607 close(3) = 0  
unknown file: Failure  
607 write(1,0x4f99e0,22) = 22  
C++ exception with description "boost::filesystem::copy_file: Function not implemented: "/home/conan/b8f3-d6a1-a97d-8301.defaults", "/home/conan/b8f3-d6a1-a97d-8301"" thrown in the test body.  
```  
  
It would be nice to have an option for fall back to other implementation, when possible.  
  
Thanks!

---

## Comment 1

> Username: Lastique  
> Created at: 2021-09-28 10:11:29 UTC  
> Url: https://github.com/boostorg/filesystem/issues/207#issuecomment-929049531  

Boost.Filesystem does not use `fallocate`. Most likely you're missing `copy_file_range` or `statx` syscalls. Duplicates https://github.com/boostorg/filesystem/issues/172.
