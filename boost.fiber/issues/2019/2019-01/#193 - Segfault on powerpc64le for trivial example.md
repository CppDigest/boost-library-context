# #193 - Segfault on powerpc64le for trivial example [Closed]

> Username: wak-google  
> Created at: 2019-01-23 01:30:23 UTC  
> Updated at: 2019-05-17 06:48:28 UTC  
> Closed at: 2019-05-17 06:48:28 UTC  
> Url: https://github.com/boostorg/fiber/issues/193  

The following code works fine on amd64 but causes segfaults on powerpc64le based platforms. I don't think this is a particularly interesting code segment but all of the code I'm using which depends on boost fibers is encountering issues with the same code path as far as I can tell.  
  
```  
#include <thread>  
#include <boost/fiber/all.hpp>  
  
int main() {  
    size_t count = std::thread::hardware_concurrency();  
    for(size_t i=1;i<count;i++) {  
        new std::thread([&count] {  
                boost::fibers::use_scheduling_algorithm<boost::fibers::algo::work_stealing>(count);  
                });  
    }  
    boost::fibers::use_scheduling_algorithm<boost::fibers::algo::work_stealing>(count);  
    return 0;  
}  
```  
Compiled with:  
boost 1.69.0  
gcc 7.3.0  
glibc 2.27  
```  
g++ test.cpp -g3 -O0 -o test -lboost_context -lboost_fiber -pthread -std=c++17  
```  
  
Fails at:  
```  
#0  0x000000000021dc18 in ?? ()  
#1  0x00007ffff7f166f0 in boost::context::fiber::swap (this=0x7fffb4020940, other=...) at ./boost/context/fiber_fcontext.hpp:329  
#2  0x00007ffff7f1648c in boost::context::fiber::fiber (this=0x7fffb4020940, other=...) at ./boost/context/fiber_fcontext.hpp:267  
#3  0x00007ffff7f16554 in boost::context::fiber::operator= (this=0x7fffb4000bf0, other=...) at ./boost/context/fiber_fcontext.hpp:272  
#4  0x00007ffff7f13228 in boost::fibers::context::<lambda(boost::context::fiber&&)>::operator()(boost::context::fiber &&) const (__closure=0x7fffb40209f8, c=...)  
    at libs/fiber/src/context.cpp:164  
#5  0x00007ffff7f151d0 in boost::context::detail::fiber_ontop<boost::context::fiber, boost::fibers::context::resume(boost::fibers::detail::spinlock_lock&)::<lambda(boost::context::fiber&&)> >(boost::context::detail::transfer_t) (t=...) at ./boost/context/fiber_fcontext.hpp:100  
#6  0x00007ffff7f1b0d4 in boost::context::detail::fiber_entry<boost::context::detail::fiber_record<boost::context::fiber, boost::context::basic_fixedsize_stack<boost::context::stack_traits> const&, std::_Bind<boost::context::fiber (boost::fibers::dispatcher_context::*(boost::fibers::dispatcher_context*, std::_Placeholder<1>))(boost::context::fiber&&)> > >(boost::context::detail::transfer_t) (t=...) at ./boost/context/fiber_fcontext.hpp:78  
#7  0x00007ffff79d1c4c in make_fcontext () at libs/context/src/asm/make_ppc64_sysv_elf_gas.S:151  
```

---

## Comment 1

> Username: wak-google  
> Created at: 2019-01-23 01:47:41 UTC  
> Url: https://github.com/boostorg/fiber/issues/193#issuecomment-456636305  

I can use an even more trivial example....  
```  
#include <boost/fiber/all.hpp>  
  
int main() {  
    boost::fibers::use_scheduling_algorithm<boost::fibers::algo::work_stealing>(1);  
    return 0;  
}  
```

---

## Comment 2

> Username: wak-google  
> Created at: 2019-01-23 01:56:54 UTC  
> Url: https://github.com/boostorg/fiber/issues/193#issuecomment-456638344  

It looks like any application using fibers should be sufficient to hit this issue, even the examples/simple.cpp will hit this.

---

## Comment 3

> Username: olk  
> Created at: 2019-01-23 05:46:52 UTC  
> Url: https://github.com/boostorg/fiber/issues/193#issuecomment-456678383  

The code for powerpc64 was contributed b several other developers.  
I've no access to a powerpc64 system.  
I guess the problem is related to the context swapping code (assembler) in boost.context.  
Could you execute/run the unit tests of boost.context (context/test)?!

---

## Comment 4

> Username: CrystalGamma  
> Created at: 2019-01-24 13:28:46 UTC  
> Url: https://github.com/boostorg/fiber/issues/193#issuecomment-457196267  

I tried the unit tests on powerpc64le (to the best of my knowledge, I have no idea how your build system works) after hitting the Nix crash referenced above, and it didn't fail. I don't have a BE system setup to test that.

---

## Comment 5

> Username: wak-google  
> Created at: 2019-01-25 00:32:16 UTC  
> Url: https://github.com/boostorg/fiber/issues/193#issuecomment-457410884  

[test.log](https://github.com/boostorg/fiber/files/2794167/test.log)

---

## Comment 6

> Username: wak-google  
> Created at: 2019-01-25 00:40:20 UTC  
> Updated at: 2019-01-25 00:40:37 UTC  
> Url: https://github.com/boostorg/fiber/issues/193#issuecomment-457412697  

```  
#0  0x0000000000000000 in ?? ()  
#1  0x0000000100011074 in boost::context::detail::transfer_t boost::context::detail::fiber_exit<boost::context::detail::fiber_record<boost::context::fiber, boost::context::basic_fixedsize_stack<boost::context::stack_traits>, test_move()::{lambda(boost::context::fiber&&)#1}> >(boost::context::detail::fiber_record<boost::context::fiber, boost::context::basic_fixedsize_stack<boost::context::stack_traits>, test_move()::{lambda(boost::context::fiber&&)#1}>) ()  
#2  0x000000010001ba4c in test_move() ()  
#3  0x000000010001fa34 in boost::detail::function::void_function_invoker0<void (*)(), void>::invoke(boost::detail::function::function_buffer&) ()  
#4  0x00000001000abf6c in boost::detail::function::function_obj_invoker0<boost::detail::forward, int>::invoke(boost::detail::function::function_buffer&) ()  
#5  0x00000001000a504c in boost::execution_monitor::catch_signals(boost::function<int ()> const&) ()  
#6  0x00000001000a5234 in boost::execution_monitor::execute(boost::function<int ()> const&) ()  
#7  0x00000001000a5d54 in boost::execution_monitor::vexecute(boost::function<void ()> const&) ()  
#8  0x000000010009db24 in boost::unit_test::unit_test_monitor_t::execute_and_translate(boost::function<void ()> const&, unsigned int) ()  
#9  0x000000010009382c in boost::unit_test::framework::state::execute_test_tree(unsigned long, unsigned int, boost::unit_test::framework::state::random_generator_helper const*) ()  
#10 0x0000000100093cfc in boost::unit_test::framework::state::execute_test_tree(unsigned long, unsigned int, boost::unit_test::framework::state::random_generator_helper const*) ()  
#11 0x0000000100093cfc in boost::unit_test::framework::state::execute_test_tree(unsigned long, unsigned int, boost::unit_test::framework::state::random_generator_helper const*) ()  
#12 0x0000000100089f30 in boost::unit_test::framework::run(unsigned long, bool) ()  
#13 0x0000000100045720 in boost::unit_test::unit_test_main(boost::unit_test::test_suite* (*)(int, char**), int, char**) ()  
#14 0x00007ffff795441c in generic_start_main (main=0x10000fa40 <main>, argc=<optimized out>, argv=0x7ffffffff198, auxvec=0x7ffffffff2a0, init=<optimized out>,  
    rtld_fini=<optimized out>, stack_end=<optimized out>, fini=<optimized out>) at ../csu/libc-start.c:310  
#15 0x00007ffff7954618 in __libc_start_main (argc=<optimized out>, argv=<optimized out>, ev=<optimized out>, auxvec=<optimized out>, rtld_fini=<optimized out>,  
    stinfo=<optimized out>, stack_on_entry=<optimized out>) at ../sysdeps/unix/sysv/linux/powerpc/libc-start.c:116  
#16 0x0000000000000000 in ?? ()  
```

---

## Comment 7

> Username: wak-google  
> Created at: 2019-01-25 01:06:03 UTC  
> Url: https://github.com/boostorg/fiber/issues/193#issuecomment-457417793  

Might be a little more helpful to do a debug build  
```  
#0  0x0000000000000000 in ?? ()  
#1  0x0000000100028da0 in boost::context::basic_fixedsize_stack<boost::context::stack_traits>::deallocate (this=0x7fffffffcdd0, sctx=...)  
    at ../../../boost/context/fixedsize_stack.hpp:65  
#2  0x0000000100022bcc in boost::context::detail::fiber_record<boost::context::fiber, boost::context::basic_fixedsize_stack<boost::context::stack_traits>, test_move()::<lambda(boost::context::fiber&&)> >::destroy(boost::context::detail::fiber_record<boost::context::fiber, boost::context::basic_fixedsize_stack<boost::context::stack_traits>, test_move()::<lambda(boost::context::fiber&&)> > *) (p=0x7ffff791ff00) at ../../../boost/context/fiber_fcontext.hpp:121  
#3  0x0000000100021a08 in boost::context::detail::fiber_record<boost::context::fiber, boost::context::basic_fixedsize_stack<boost::context::stack_traits>, test_move()::<lambda(boost::context::fiber&&)> >::deallocate(void) (this=0x7ffff791ff00) at ../../../boost/context/fiber_fcontext.hpp:136  
#4  0x000000010002014c in boost::context::detail::fiber_exit<boost::context::detail::fiber_record<boost::context::fiber, boost::context::basic_fixedsize_stack<boost::context::stack_traits>, test_move()::<lambda(boost::context::fiber&&)> > >(boost::context::detail::transfer_t) (t=...) at ../../../boost/context/fiber_fcontext.hpp:66  
#5  0x00000001000249f4 in boost::context::fiber::resume() && (this=0x7fffffffcf78) at ../../../boost/context/fiber_fcontext.hpp:283  
#6  0x0000000100011a74 in test_move () at test_fiber.cpp:150  
#7  0x000000010002810c in boost::detail::function::void_function_invoker0<void (*)(), void>::invoke (function_ptr=...) at ../../../boost/function/function_template.hpp:117  
#8  0x00000001000ec874 in boost::function0<void>::operator() (this=0x1001d8398) at ./boost/function/function_template.hpp:763  
#9  0x0000000100129020 in boost::detail::forward::operator() (this=0x7fffffffe080) at ./boost/test/impl/execution_monitor.ipp:1312  
#10 0x000000010012e094 in boost::detail::function::function_obj_invoker0<boost::detail::forward, int>::invoke (function_obj_ptr=...) at ./boost/function/function_template.hpp:137  
#11 0x000000010012bd70 in boost::function0<int>::operator() (this=0x7fffffffe078) at ./boost/function/function_template.hpp:764  
#12 0x000000010012a40c in boost::detail::do_invoke<boost::shared_ptr<boost::detail::translator_holder_base>, boost::function<int ()> >(boost::shared_ptr<boost::detail::translator_holder_base> const&, boost::function<int ()> const&) (tr=..., F=...) at ./boost/test/impl/execution_monitor.ipp:286  
#13 0x000000010012585c in boost::execution_monitor::catch_signals(boost::function<int ()> const&) (this=0x1001b0df0 <boost::unit_test::unit_test_monitor_t::instance()::the_inst>,  
    F=...) at ./boost/test/impl/execution_monitor.ipp:875  
#14 0x0000000100125a90 in boost::execution_monitor::execute(boost::function<int ()> const&) (this=0x1001b0df0 <boost::unit_test::unit_test_monitor_t::instance()::the_inst>, F=...)  
    at ./boost/test/impl/execution_monitor.ipp:1214  
#15 0x0000000100126b30 in boost::execution_monitor::vexecute(boost::function<void ()> const&) (this=0x1001b0df0 <boost::unit_test::unit_test_monitor_t::instance()::the_inst>, F=...)  
    at ./boost/test/impl/execution_monitor.ipp:1321  
#16 0x000000010011bc88 in boost::unit_test::unit_test_monitor_t::execute_and_translate(boost::function<void ()> const&, unsigned int) (  
    this=0x1001b0df0 <boost::unit_test::unit_test_monitor_t::instance()::the_inst>, func=..., timeout=0) at ./boost/test/impl/unit_test_monitor.ipp:49  
#17 0x00000001000eb11c in boost::unit_test::framework::state::execute_test_tree (  
    this=0x1001b0b98 <boost::unit_test::framework::impl::(anonymous namespace)::s_frk_state()::the_inst>, tu_id=65536, timeout=0, p_random_generator=0x7fffffffe492)  
    at ./boost/test/impl/framework.ipp:789  
#18 0x00000001000eaba8 in boost::unit_test::framework::state::execute_test_tree (  
    this=0x1001b0b98 <boost::unit_test::framework::impl::(anonymous namespace)::s_frk_state()::the_inst>, tu_id=2, timeout=0, p_random_generator=0x7fffffffe792)  
    at ./boost/test/impl/framework.ipp:737  
#19 0x00000001000eaba8 in boost::unit_test::framework::state::execute_test_tree (  
    this=0x1001b0b98 <boost::unit_test::framework::impl::(anonymous namespace)::s_frk_state()::the_inst>, tu_id=1, timeout=0, p_random_generator=0x0)  
    at ./boost/test/impl/framework.ipp:737  
#20 0x00000001000e2d3c in boost::unit_test::framework::run (id=1, continue_test=true) at ./boost/test/impl/framework.ipp:1631  
#21 0x000000010005d444 in boost::unit_test::unit_test_main (init_func=0x100018814 <init_unit_test_suite(int, char**)>, argc=1, argv=0x7ffffffff1a8)  
    at ./boost/test/impl/unit_test_main.ipp:247  
#22 0x000000010005d918 in main (argc=1, argv=0x7ffffffff1a8) at ./boost/test/impl/unit_test_main.ipp:303  
```

---

## Comment 8

> Username: wak-google  
> Created at: 2019-01-25 01:37:53 UTC  
> Url: https://github.com/boostorg/fiber/issues/193#issuecomment-457424127  

Something is entirely wrong here as we are jumping to null inside of std::free()  
65              std::free( vp);  
(gdb) print vp  
$1 = (void *) 0x7ffff7900010

---

## Comment 9

> Username: wak-google  
> Created at: 2019-01-25 03:26:43 UTC  
> Url: https://github.com/boostorg/fiber/issues/193#issuecomment-457442929  

Something is breaking the indirect call into the hidden_proto __libc_free from the plt_call.free

---

## Comment 10

> Username: wak-google  
> Created at: 2019-01-25 03:31:00 UTC  
> Url: https://github.com/boostorg/fiber/issues/193#issuecomment-457443531  

at the point of doing bctr, ctr is 0x0  
```  
   │0x100010c60 <00000019.plt_call.free@@GLIBC_2.17>                                                                                                        std     r2,24(r1)        │  
   │0x100010c64 <00000019.plt_call.free@@GLIBC_2.17+4>                                                                                                      addis   r12,r2,-1        │  
   │0x100010c68 <00000019.plt_call.free@@GLIBC_2.17+8>                                                                                                      ld      r12,31568(r12)   │  
   │0x100010c6c <00000019.plt_call.free@@GLIBC_2.17+12>                                                                                                     mtctr   r12  
0x100010c70 <00000019.plt_call.free@@GLIBC_2.17+16>                                                                                                     bctr  
```

---

## Comment 11

> Username: wak-google  
> Created at: 2019-01-25 04:00:56 UTC  
> Url: https://github.com/boostorg/fiber/issues/193#issuecomment-457447923  

Doing a lookup on r12 + 31568 prior to the load I'm getting   
(gdb) print *(void**)0x1001b4138  
$3 = (void *) 0x0  
  
on a working program where it would load r12 + 32760 I get  
(gdb) print *(void**)0x10001fef8  
$5 = (void *) 0x7ffff7df3bd0 <__GI___libc_free>  
  
so it looks like the jump table offsets might be okay but the table is getting zero'd out?

---

## Comment 12

> Username: wak-google  
> Created at: 2019-01-25 04:26:15 UTC  
> Url: https://github.com/boostorg/fiber/issues/193#issuecomment-457451575  

Okay so we are using a different PLT during the corruption  
  
```  
0x0000000100010068 in 00000019.plt_call.malloc@@GLIBC_2.17 ()  
(gdb) print *(void**)($r12 + 32408)  
$3 = (void *) 0x7ffff79e2f80 <__GI___libc_malloc>  
(gdb) print *(void**)($r12 + 31568)  
$4 = (void *) 0x7ffff79e3bd0 <__GI___libc_free>  
(gdb) print (void*)($r12 + 31568)  
$6 = (void *) 0x1001af850  
(gdb) continue  
Continuing.  
  
Breakpoint 1, 0x0000000100010c60 in 00000019.plt_call.free@@GLIBC_2.17 ()  
(gdb) stepi  
0x0000000100010c64 in 00000019.plt_call.free@@GLIBC_2.17 ()  
0x0000000100010c68 in 00000019.plt_call.free@@GLIBC_2.17 ()  
(gdb)  print (void*)($r12 + 31568)  
$9 = (void *) 0x1001b4138  
(gdb) print *(void**)($r12 + 31568)  
$10 = (void *) 0x0  
```

---

## Comment 13

> Username: olk  
> Created at: 2019-01-25 08:22:23 UTC  
> Url: https://github.com/boostorg/fiber/issues/193#issuecomment-457492878  

It's a problem in boost.context PowerPC implementation - unfortunately I should debug the example code in order to locate the bug - but without a PowerPC64 system... (maybe I get a qemu-powerpc64-image running).

---

## Comment 14

> Username: q66  
> Created at: 2019-03-14 23:03:59 UTC  
> Url: https://github.com/boostorg/fiber/issues/193#issuecomment-473098600  

@olk any news about this? I can provide access to a ppc64le based system is necessary

---

## Comment 15

> Username: q66  
> Created at: 2019-03-14 23:20:50 UTC  
> Url: https://github.com/boostorg/fiber/issues/193#issuecomment-473102019  

Here are bits of log when the code was discussed, maybe it'll help  
  
```  
<Bdragon> I still say the jump code is still problematic.. you can't just move r1 like that without updating the back chain, it has to be updated atomically unless you're specifically making alternate data available to the stack unwinder somehow.  
<Bdragon> and if it's breaking the backchain apart on purpose, it had better be zeroing it  
<Bdragon> oh, the damn diagram's upside down  
<Bdragon> yeah, I don't think this code is paying attention to who owns what parts of the stack  
<Bdragon> need to rework the stack usage slightly so it isn't saving stuff in positions that are callee-overwritten  
<Bdragon> or rather  
<Bdragon> the code at least will be *understandable* if it is a bit more idiomatic.  
<Bdragon> main gist of things are though: 0(%r1) is the backchain, don't use it for other purposes. the dwords at 8(%r1) and 16(%r1) are volatile, and 24(%r1) is reserved for backing up %r2.  
<Bdragon> elfv1 is... more complicated.  
```

---

## Comment 16

> Username: olk  
> Created at: 2019-03-15 09:21:31 UTC  
> Url: https://github.com/boostorg/fiber/issues/193#issuecomment-473214412  

> any news about this?  
  
unfortunately no, I'm too busy

---

## Comment 17

> Username: q66  
> Created at: 2019-03-18 01:16:38 UTC  
> Url: https://github.com/boostorg/fiber/issues/193#issuecomment-473736835  

I also further found out that in my minimal testcase with a simple coroutine implementation, everything works except 1 case, which is when a paused coroutine is force destroyed (i.e. `ontop_fcontext` is called to forcibly unwind the stack), otherwise the code seems to be working fine. Which makes me suspect that `ontop_fcontext` is the real culprit in this bug, and nothing else. The original stacktrace also seems to indicate the same behavior with the fiber test

---

## Comment 18

> Username: shawnanastasio  
> Created at: 2019-05-04 01:25:29 UTC  
> Url: https://github.com/boostorg/fiber/issues/193#issuecomment-489281989  

I have submitted a fix [here](https://github.com/boostorg/context/pull/106). The `ontop_fcontext` function was not setting r12 to the callback entrypoint in accordance with the ABI.

---

## Comment 19

> Username: olk  
> Created at: 2019-05-17 06:48:28 UTC  
> Url: https://github.com/boostorg/fiber/issues/193#issuecomment-493341681  

ty
