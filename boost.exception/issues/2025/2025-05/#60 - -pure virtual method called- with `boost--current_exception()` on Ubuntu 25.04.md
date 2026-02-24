# #60 - "pure virtual method called" with `boost::current_exception()` on Ubuntu 25.04 [Closed]

> Username: Pesa  
> Created at: 2025-05-02 03:03:05 UTC  
> Updated at: 2026-02-04 17:23:55 UTC  
> Closed at: 2026-02-04 17:23:55 UTC  
> Url: https://github.com/boostorg/exception/issues/60  

I'm getting an odd crash when calling `boost::current_exception()` on Ubuntu 25.04 (x86_64, Boost 1.83.0). The same exact code worked fine on previous versions, notably it was working on Ubuntu 24.10 that should have the same version of gcc (14.2.0).  
  
I was able to create a minimized program (below) that reproduces the issue reliably when built with `-O0` or `-Og`  
  
```cpp  
#include <stdexcept>  
#include <boost/exception_ptr.hpp>  
  
int main()  
{  
  try {  
    throw std::out_of_range("foo");  
  }  
  catch (const std::logic_error&) {  
    boost::current_exception();  
  }  
}  
```  
  
Build/run with `g++ -std=c++17 -O0 -g3 crash.cpp && ./a.out`  
  
```  
[Thread debugging using libthread_db enabled]  
Using host libthread_db library "/lib/x86_64-linux-gnu/libthread_db.so.1".  
pure virtual method called  
terminate called after throwing an instance of 'std::out_of_range'  
  what():  foo  
  
Program received signal SIGABRT, Aborted.  
__pthread_kill_implementation (threadid=<optimized out>, signo=6, no_tid=0) at ./nptl/pthread_kill.c:44  
warning: 44     ./nptl/pthread_kill.c: No such file or directory  
(gdb) bt full  
#0  __pthread_kill_implementation (threadid=<optimized out>, signo=6, no_tid=0) at ./nptl/pthread_kill.c:44  
        tid = <optimized out>  
        ret = 0  
        pd = <optimized out>  
        old_mask = {__val = {0}}  
        ret = <optimized out>  
        pd = <optimized out>  
        old_mask = <optimized out>  
        ret = <optimized out>  
        tid = <optimized out>  
        ret = <optimized out>  
        resultvar = <optimized out>  
        resultvar = <optimized out>  
        __arg3 = <optimized out>  
        __arg2 = <optimized out>  
        __arg1 = <optimized out>  
        _a3 = <optimized out>  
        _a2 = <optimized out>  
        _a1 = <optimized out>  
        __futex = <optimized out>  
        resultvar = <optimized out>  
        __arg3 = <optimized out>  
        __arg2 = <optimized out>  
        __arg1 = <optimized out>  
        _a3 = <optimized out>  
        _a2 = <optimized out>  
        _a1 = <optimized out>  
        __futex = <optimized out>  
        __private = <optimized out>  
        __oldval = <optimized out>  
#1  __pthread_kill_internal (threadid=<optimized out>, signo=6) at ./nptl/pthread_kill.c:89  
No locals.  
#2  __GI___pthread_kill (threadid=<optimized out>, signo=signo@entry=6) at ./nptl/pthread_kill.c:100  
No locals.  
#3  0x00007ffff78456de in __GI_raise (sig=sig@entry=6) at ../sysdeps/posix/raise.c:26  
        ret = <optimized out>  
#4  0x00007ffff78288cd in __GI_abort () at ./stdlib/abort.c:73  
        act = {__sigaction_handler = {sa_handler = 0x0, sa_sigaction = 0x0}, sa_mask = {__val = {0, 0, 0, 0, 0, 140737347921251, 140737347911632, 140737488344912, 140737346375855, 1, 140737347921120, 10,   
              93824992359488, 93824992277640, 140737354125312, 140737488344960}}, sa_flags = -141976989, sa_restorer = 0x555555573428}  
#5  0x00007ffff7cab05f in ?? () from /lib/x86_64-linux-gnu/libstdc++.so.6  
No symbol table info available.  
#6  0x00007ffff7cc10aa in ?? () from /lib/x86_64-linux-gnu/libstdc++.so.6  
No symbol table info available.  
#7  0x00007ffff7caaa9e in std::terminate() () from /lib/x86_64-linux-gnu/libstdc++.so.6  
No symbol table info available.  
#8  0x00007ffff7cc1f83 in __cxa_pure_virtual () from /lib/x86_64-linux-gnu/libstdc++.so.6  
No symbol table info available.  
#9  0x0000555555557ed8 in boost::exception_detail::wrap_exception_ptr (e=...) at /usr/include/boost/exception/detail/exception_ptr.hpp:68  
        base = @0x7fffffffd840: {_vptr.clone_base = 0x55555555fa68 <vtable for boost::exception_detail::clone_base+16>}  
#10 0x0000555555557f92 in boost::exception_ptr::exception_ptr (this=0x7fffffffd920, e=...) at /usr/include/boost/exception/detail/exception_ptr.hpp:89  
No locals.  
#11 0x00005555555585e7 in boost::exception_detail::current_exception_impl () at /usr/include/boost/exception/detail/exception_ptr.hpp:491  
        e = 0x0  
        __PRETTY_FUNCTION__ = "boost::exception_ptr boost::exception_detail::current_exception_impl()"  
#12 0x0000555555558745 in boost::current_exception () at /usr/include/boost/exception/detail/exception_ptr.hpp:514  
        ret = {ptr_ = {px = 0x0, pn = {pi_ = 0x0}}}  
        __PRETTY_FUNCTION__ = "boost::exception_ptr boost::current_exception()"  
#13 0x00005555555574d0 in main () at crash.cpp:10  
No locals.  
(gdb)   
```  
  
I could not reproduce with clang 20 on the same platform. Raising the optimization level to `-O1` or `-O2` also makes the crash disappear (so, it could be a gcc miscompilation).

---

## Comment 1

> Username: zajo  
> Created at: 2025-10-11 21:01:05 UTC  
> Url: https://github.com/boostorg/exception/issues/60#issuecomment-3393655755  

Sorry I realize this is an old post, I've been busy lately haven't been able to pay attention to this. I don't see a crash on godbolt. Maybe the configuration is wrong, feel free to tweak and post another godbolt link: https://godbolt.org/z/oj71hh8c6
