# #253 - Add support for growing `mapped_region` [Open]

> Username: Lastique  
> Created at: 2025-03-10 00:41:43 UTC  
> Updated at: 2025-03-10 01:00:41 UTC  
> Url: https://github.com/boostorg/interprocess/issues/253  

[Linux](https://linux.die.net/man/2/mremap), [NetBSD](https://man.freebsd.org/cgi/man.cgi?query=mremap&apropos=0&sektion=0&manpath=NetBSD+10.1&arch=default&format=html) and [Solaris](https://docs.oracle.com/cd/E88353_01/html/E37841/mremap-2.html) have a `mremap` system call that allows to opportunistically grow or relocate the existing memory mapping. There also seems to be a way to achieve this on [Windows](https://stackoverflow.com/questions/17197615/no-mremap-for-windows), but I'm not familiar with that API.  
  
Please, add support for this functionality (specifically, growing and/or relocating a memory mapping) to the `mapped_region` class. For example, add a `grow_by` method that would try to increase the size of the mapped region at the same address and `remap` to relocate the region at a different address and size. On platforms that don't support this functionality, the new methods would always fail. This would also be indicated at compile time by some library-defined config macro.
