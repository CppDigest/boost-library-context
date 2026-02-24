# #150 src/event.cpp: fic build on riscv32 [Closed]

> Username: ffontaine  
> Created at: 2021-05-29 08:40:36 UTC  
> Updated at: 2021-11-06 16:46:25 UTC  
> Closed at: 2021-05-29 14:11:16 UTC  
> Url: https://github.com/boostorg/log/pull/150  

riscv32 fails to build because `__NR_futex` is not defined on this architecture:  
  
```  
libs/log/src/event.cpp: In member function 'void boost::log::v2_mt_posix::aux::futex_based_event::wait()':  
libs/log/src/event.cpp:38:29: error: '__NR_futex' was not declared in this scope  
   38 | #define BOOST_LOG_SYS_FUTEX __NR_futex  
      |                             ^~~~~~~~~~  
```  
  
Fixes:  
 - http://autobuild.buildroot.org/results/8c8135fd7c0517c66c9b3975c494da6d7934cc1b  
  
Signed-off-by: Fabrice Fontaine <fontaine.fabrice@gmail.com>

---

## Comment 1

> Username: Lastique  
> Created_at: 2021-05-29 14:11:36 UTC  
> Url: https://github.com/boostorg/log/pull/150#issuecomment-850840054  

Thanks. Merged with a few corrections.

---

## Review 2 [Commented]

> Username: Gnosisuy  
> Created_at: 2021-11-06 16:46:25 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/log/pull/150#pullrequestreview-799459228  

[](url)

---
