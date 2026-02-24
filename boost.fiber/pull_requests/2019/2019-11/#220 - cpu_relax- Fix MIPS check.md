# #220 cpu_relax: Fix MIPS check [Merged]

> Username: neheb  
> Created at: 2019-11-15 20:43:59 UTC  
> Updated at: 2019-11-15 21:30:40 UTC  
> Merged at: 2019-11-15 21:26:19 UTC  
> Closed at: 2019-11-15 21:26:20 UTC  
> Url: https://github.com/boostorg/fiber/pull/220  

BOOST_ARCH_MIPS as a macro is totally broken. It always gets defined to either 32  
or 64 with a bunch of zeroes depending on the architecture.  
  
Use GCC's internal define to check the architecture properly.  
  
Signed-off-by: Rosen Penev <rosenp@gmail.com>

---

## Comment 1

> Username: olk  
> Created_at: 2019-11-15 21:26:24 UTC  
> Url: https://github.com/boostorg/fiber/pull/220#issuecomment-554534726  

ty

---
