# #292 futex: fix build on 32-bit architectures using 64-bit time_t [Merged]

> Username: kraj  
> Created at: 2022-02-21 06:17:02 UTC  
> Updated at: 2022-02-21 06:18:07 UTC  
> Merged at: 2022-02-21 06:18:01 UTC  
> Closed at: 2022-02-21 06:18:01 UTC  
> Url: https://github.com/boostorg/fiber/pull/292  

Fix the following build failure on 32-bit architectures using 64-bit  
time_t (e.g. riscv32):  
| ./boost/fiber/detail/futex.hpp:31:23: error: use of undeclared identifier 'SYS_futex'  
|     return ::syscall( SYS_futex, addr, op, x, nullptr, nullptr, 0);  
|                       ^  
| 1 error generated.  
  
Signed-off-by: Khem Raj <raj.khem@gmail.com>

---

## Comment 1

> Username: olk  
> Created_at: 2022-02-21 06:18:06 UTC  
> Url: https://github.com/boostorg/fiber/pull/292#issuecomment-1046512022  

ty

---
