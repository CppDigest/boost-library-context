# #79 - Some troubles trying to contruct an execution_context (v2) while sitting on the stack of another one [Closed]

> Username: matovitch  
> Created at: 2018-04-29 12:46:26 UTC  
> Updated at: 2018-04-29 15:56:40 UTC  
> Closed at: 2018-04-29 15:21:59 UTC  
> Url: https://github.com/boostorg/context/issues/79  

Hello,  
  
I use boost_context for a toy project of coroutine scheduler to be found here:   
https://github.com/matovitch/coscheV3.  
  
It works as expected as long as don't try to nest my coroutine. You can see an example here:  
https://github.com/matovitch/coscheV3/blob/master/examples/nested.cpp.  
  
The example appears to be working fine, but valgrind complains of invalid reads in the context contructor:  
  
```  
==21651== Use of uninitialised value of size 8  
==21651==    at 0x40CBB7: jump_fcontext (jump_x86_64_sysv_elf_gas.S:64)  
==21651==    by 0x40DD3D: boost::context::v2::execution_context<cosche::task::Abstract*>::execution_context<boost::context::v2::execution_context<cosche::task::Abstract*> (&)(boost::context::v2::execution_context<cosche::task::Abstract*>&&, cosche::task::Abstract*), , void>(boost::context::v2::execution_context<cosche::task::Abstract*> (&)(boost::context::v2::execution_context<cosche::task::Abstract*>&&, cosche::task::Abstract*)) (execution_context_v2.hpp:167)  
==21651==    by 0x40DCA4: cosche::task::Abstract::Abstract(cosche::scheduler::Abstract&) (task.cpp:26)  
==21651==    by 0x4092F6: cosche::TTask<void>::TTask(cosche::scheduler::Abstract&) (in /home/cbrugel/workspace/C++/coscheV3/build/examples/nested)  
==21651==    by 0x40727C: cosche::TTask<void>& cosche::pool::TFactory<cosche::TTask<void>, 4096ul>::make<cosche::TScheduler<4096ul, 4096ul, 4096ul>&>(cosche::TScheduler<4096ul, 4096ul, 4096ul>&) (pool_factory.hpp:31)  
==21651==    by 0x401D84: cosche::TNode<cosche::task::Abstract*>& cosche::TScheduler<4096ul, 4096ul, 4096ul>::makeTask<void>() (scheduler.hpp:45)  
==21651==    by 0x401C17: main::$_0::operator()() const (nested.cpp:23)  
==21651==    by 0x401ACC: std::_Function_handler<void (), main::$_0>::_M_invoke(std::_Any_data const&) (std_function.h:316)  
==21651==    by 0x40982D: std::function<void ()>::operator()() const (in /home/cbrugel/workspace/C++/coscheV3/build/examples/nested)  
==21651==    by 0x40AA6C: void std::__invoke_impl<void, std::function<void ()>&>(std::__invoke_other, std::function<void ()>&) (in /home/cbrugel/workspace/C++/coscheV3/build/examples/nested)  
==21651==    by 0x40AA3C: std::__invoke_result<std::function<void ()>&>::type std::__invoke<std::function<void ()>&>(std::function<void ()>&) (in /home/cbrugel/workspace/C++/coscheV3/build/examples/nested)  
==21651==    by 0x40AA08: void std::_Bind<std::function<void ()> ()>::__call<void>(std::tuple<>&&, std::_Index_tuple<>) (in /home/cbrugel/workspace/C++/coscheV3/build/examples/nested)  
```  
  
Here is a minimal example producing the same result.  
  
```c++  
#include <iostream>  
  
#include "cosche/scheduler.hpp"  
#include "cosche/utils.hpp"  
  
static constexpr std::size_t ABSTRACT_TASK_ALLOCATOR_BUFFER_SIZE = 4096;  
static constexpr std::size_t CONCRETE_TASK_ALLOCATOR_BUFFER_SIZE = 4096;  
static constexpr std::size_t        FUTURE_ALLOCATOR_BUFFER_SIZE = 4096;  
  
using Scheduler = cosche::TScheduler<ABSTRACT_TASK_ALLOCATOR_BUFFER_SIZE,   
                                     CONCRETE_TASK_ALLOCATOR_BUFFER_SIZE,   
                                            FUTURE_ALLOCATOR_BUFFER_SIZE>;  
  
int main()  
{  
    Scheduler scheduler;  
  
    auto&& rootTask = scheduler.makeTask<void>();  
  
    std::function<void()> rootWork =   
        [&]()  
        {  
            auto&& leafTask = scheduler.makeTask<void>();  
        };  
  
    cosche::assignWork(rootTask, std::move(rootWork));  
  
    scheduler.run();  
  
    cosche::cleanUp();  
  
    return 0;  
}  
  
```  
We could dismiss it as a false positive. But it doesn't seem to be because after nesting only 6 times with a [binary-tree-like task graph](https://github.com/matovitch/coscheV3/blob/master/examples/tree.cpp), it cores. I believed to have an explanation but I had read the documentation incorrectly. Still not sure about these invalid reads. Can you help me on this ? Do you understand what happen there ?  
  
Thanks a lot for your help and for taking the time to read this.

---

## Comment 1

> Username: olk  
> Created at: 2018-04-29 14:31:06 UTC  
> Url: https://github.com/boostorg/context/issues/79#issuecomment-385255625  

maybe the stack is too small

---

## Comment 2

> Username: matovitch  
> Created at: 2018-04-29 14:38:45 UTC  
> Updated at: 2018-04-29 14:39:52 UTC  
> Url: https://github.com/boostorg/context/issues/79#issuecomment-385256096  

But the 2 stacks are allocated on the heap with the stack allocator isn't it ?  
  
FYI, here are the value of the registers (it's the popping of RBP valgrind complains about):  
  
```  
(gdb) i r  
rax            0x7fffffffdb08	140737488345864  
rbx            0x0	0  
rcx            0x0	0  
rdx            0x62ac60	6466656  
rsi            0x7fffffffd8e8	140737488345320  
rdi            0x7fffffffdb08	140737488345864  
rbp            0x7fffffffdb60	0x7fffffffdb60  
rsp            0x7fffffffda70	0x7fffffffda70  
r8             0xffffffffffffffff	-1  
r9             0x0	0  
r10            0x22	34  
r11            0x246	582  
r12            0x4016c0	4200128  
r13            0x7fffffffdc40	140737488346176  
r14            0x0	0  
r15            0x0	0  
rip            0x4017e4	0x4017e4 <main()+42>  
eflags         0x202	[ IF ]  
cs             0x33	51  
ss             0x2b	43  
ds             0x0	0  
es             0x0	0  
fs             0x0	0  
gs             0x0	0  
(gdb) c  
Continuing.  
  
Breakpoint 2, <lambda()>::operator()(void) const (__closure=0x64ae20) at ../examples/nested.cpp:23  
23	            auto&& leafTask = scheduler.makeTask<void>();  
(gdb) i r  
rax            0x64ae20	6598176  
rbx            0x40c40c	4244492  
rcx            0x0	0  
rdx            0x64ae20	6598176  
rsi            0x64aa97	6597271  
rdi            0x64ae20	6598176  
rbp            0x64a9a0	0x64a9a0  
rsp            0x64a980	0x64a980  
r8             0x40c863	4245603  
r9             0x0	0  
r10            0xa42	2626  
r11            0x7ffff7adeaf0	140737348758256  
r12            0x7ffff7f15018	140737353175064  
r13            0x0	0  
r14            0x0	0  
r15            0x0	0  
rip            0x4017a4	0x4017a4 <<lambda()>::operator()(void) const+12>  
eflags         0x202	[ IF ]  
cs             0x33	51  
ss             0x2b	43  
ds             0x0	0  
es             0x0	0  
fs             0x0	0  
gs             0x0	0  
```  
  
edit: The first display is the first (non-nested) makeTask.

---

## Comment 3

> Username: matovitch  
> Created at: 2018-04-29 15:21:59 UTC  
> Url: https://github.com/boostorg/context/issues/79#issuecomment-385258899  

Mea culpa. Nothing to do with boost_context, I had forgotten a return [there](https://github.com/matovitch/coscheV3/blob/master/include/cosche/pool_allocator.hpp#L48), so it failed on the allocation of the second buffer by my home-made allocator, I should have looked at the back-trace better. Sorry for disturbing you. :(  
  
Thanks and have a nice day !

---

## Comment 4

> Username: matovitch  
> Created at: 2018-04-29 15:39:46 UTC  
> Url: https://github.com/boostorg/context/issues/79#issuecomment-385260120  

Now ~1s for more than half a million tasks (1<<19) in a binary-tree-like DAG on my old 4300FX (on a single core of course). Not too bad ! I will try to profile to see what took most of the time. Pretty sure it's the context switch but cannot be too sure. Sorry again for bothering you and thanks for your work ! :)

---

## Comment 5

> Username: olk  
> Created at: 2018-04-29 15:52:46 UTC  
> Url: https://github.com/boostorg/context/issues/79#issuecomment-385260968  

I guess that the memory allcoation/deallocation cosnumes most of the time.  
(at my E5-2620 v4 a pure context switch costs 9 cpu cycles)

---

## Comment 6

> Username: matovitch  
> Created at: 2018-04-29 15:55:35 UTC  
> Updated at: 2018-04-29 15:56:40 UTC  
> Url: https://github.com/boostorg/context/issues/79#issuecomment-385261159  

I need to go, but it seems you're right: https://ibb.co/bYbpic.  
  
edit: I am even wondering if there isn't something wrong. There are a lot of allocations.
