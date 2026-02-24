# #173 - statx syscall on boost-1.75 [Closed]

> Username: skypool  
> Created at: 2021-01-15 17:22:18 UTC  
> Updated at: 2021-04-01 11:54:06 UTC  
> Closed at: 2021-01-15 19:50:51 UTC  
> Url: https://github.com/boostorg/filesystem/issues/173  

I'm using boost-1.75. On a Linux Kernel < 4.11 the statx is not yet available but BOOST_FILESYSTEM_HAS_STATX_SYSCALL is nevertheless set. It seems the code in 'has_statx_syscall.cpp' has no effect.

---

## Comment 1

> Username: Lastique  
> Created at: 2021-01-15 19:50:51 UTC  
> Url: https://github.com/boostorg/filesystem/issues/173#issuecomment-761164189  

You're probably building with kernel headers not matching the kernel you're running the compiled binaries on. Duplicates https://github.com/boostorg/filesystem/issues/172 and https://github.com/boostorg/filesystem/issues/164.

---

## Comment 2

> Username: paresy  
> Created at: 2021-04-01 11:54:06 UTC  
> Url: https://github.com/boostorg/filesystem/issues/173#issuecomment-811855245  

This also fails for any older docker builds: docker/for-linux#208  
  
If you have an outdated docker version (e.g. you use Docker on Synology) upgrading to Boost 1.75 will fail miserably.
