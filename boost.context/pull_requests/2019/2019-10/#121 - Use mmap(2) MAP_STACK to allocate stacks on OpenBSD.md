# #121 Use mmap(2) MAP_STACK to allocate stacks on OpenBSD [Merged]

> Username: kernigh  
> Created at: 2019-10-04 01:37:57 UTC  
> Updated at: 2019-10-04 04:28:05 UTC  
> Merged at: 2019-10-04 04:28:05 UTC  
> Closed at: 2019-10-04 04:28:05 UTC  
> Url: https://github.com/boostorg/context/pull/121  

Since OpenBSD 6.4 (https://www.openbsd.org/64.html), the stack pointer  
must point to MAP_STACK memory, or the kernel may kill the process  
with a signal.  All stack allocators must pass MAP_STACK to mmap(2).  
  
Define BOOST_CONTEXT_USE_MAP_STACK on OpenBSD, and don't define it on  
other systems.  This doesn't check for old versions of OpenBSD without  
MAP_STACK; but OpenBSD has stopped maintaining versions before 6.4.  
  
If BOOST_CONTEXT_USE_MAP_STACK is defined, then cause the stack  
allocators to pass MAP_STACK to mmap(2):  
  
 - fixedsize_stack uses mmap/munmap instead of malloc/free.  This  
   comes from a patch in OpenBSD Ports.  
  
 - protected_fixedsize_stack adds MAP_STACK to the mmap flags (as it  
   does in OpenBSD Ports).  Assume that systems with MAP_STACK also  
   have MAP_ANON; this is true on OpenBSD.  Delete POSIX comment,  
   because I can't find MAP_ANON nor MAP_ANONYMOUS in POSIX, so these  
   mmap calls don't conform to POSIX.  
  
 - pooled_fixedsize_stack can't call munmap, because the pool's free  
   doesn't know the allocation's size.  Instead use posix_memalign to  
   allocate memory, then mmap to replace the pages with MAP_STACK  
   pages, so the pool's free may call std::free.  
  
OpenBSD has no <ucontext.h>, so edit test/Jamfile.v2 to skip ucontext  
tests on OpenBSD.  
  
This commit and https://github.com/boostorg/test/pull/231 causes  
libs/context/test `b2 full` to pass on OpenBSD 6.5 for 64-bit x86.  
`b2 fc` fails because the allocator in test_fcontext.cpp does not use  
MAP_STACK.  The tests seem not to cover pooled_fixedsize_stack nor  
protected_fixedsize_stack, but they still pass when I temporarily hack  
callcc to use those allocators instead of fixedsize_stack.

---

## Comment 1

> Username: olk  
> Created_at: 2019-10-04 04:27:55 UTC  
> Url: https://github.com/boostorg/context/pull/121#issuecomment-538226566  

nice - ty

---
