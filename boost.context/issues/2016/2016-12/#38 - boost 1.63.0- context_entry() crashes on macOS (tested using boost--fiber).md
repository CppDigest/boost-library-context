# #38 - boost 1.63.0: context_entry() crashes on macOS (tested using boost::fiber) [Closed]

> Username: tsandmann  
> Created at: 2016-12-30 01:30:25 UTC  
> Updated at: 2017-01-10 08:11:19 UTC  
> Closed at: 2017-01-08 17:50:08 UTC  
> Url: https://github.com/boostorg/context/issues/38  

The following simple test program crashes on macOS (10.12.2; clang-800.0.42.1) with boost 1.63.0:  
```  
#include <iostream>  
#include <boost/fiber/all.hpp>  
  
int main() {  
    std::cout << "main()" << std::endl;  
    boost::fibers::fiber f { [] () { std::cout << "fiber test." << std::endl; } };  
    f.join();  
    std::cout << "main() done." << std::endl;  
  
    return 0;  
}  
// compile with: clang++ -o main main.cpp --std=c++14 -lboost_fiber-mt -lboost_context-mt   
```  
  
Debugger output:  
```  
% lldb main  
(lldb) target create "main"  
Current executable set to 'main' (x86_64).  
(lldb) run  
Process 37616 launched: '/Users/ts/src/fibertest/main' (x86_64)  
main()  
Process 37616 stopped  
* thread #1: tid = 0x327891, 0x00000001000a3bc7 libboost_fiber-mt.dylib`_ZN5boost7context6detail13context_entryINS1_6recordINS0_17execution_contextIJPNS_6fibers6detail6data_tEEEENS0_21basic_fixedsize_stackINS0_12stack_traitsEEEZNS5_7contextC1ENS5_20dispatcher_context_tERKNS0_12preallocatedERKSC_PNS5_9schedulerEE3$_0JEEEEEvNS1_10transfer_tE + 23, queue = 'com.apple.main-thread', stop reason = EXC_BAD_ACCESS (code=1, address=0x10)  
	frame #0: 0x00000001000a3bc7 libboost_fiber-mt.dylib`_ZN5boost7context6detail13context_entryINS1_6recordINS0_17execution_contextIJPNS_6fibers6detail6data_tEEEENS0_21basic_fixedsize_stackINS0_12stack_traitsEEEZNS5_7contextC1ENS5_20dispatcher_context_tERKNS0_12preallocatedERKSC_PNS5_9schedulerEE3$_0JEEEEEvNS1_10transfer_tE + 23  
libboost_fiber-mt.dylib`_ZN5boost7context6detail13context_entryINS1_6recordINS0_17execution_contextIJPNS_6fibers6detail6data_tEEEENS0_21basic_fixedsize_stackINS0_12stack_traitsEEEZNS5_7contextC1ENS5_20dispatcher_context_tERKNS0_12preallocatedERKSC_PNS5_9schedulerEE3$_0JEEEEEvNS1_10transfer_tE:  
->  0x1000a3bc7 <+23>: movq   0x10(%r15), %rcx  
    0x1000a3bcb <+27>: movq   0x28(%rcx), %rdi  
    0x1000a3bcf <+31>: movq   %rax, 0x28(%rcx)  
    0x1000a3bd3 <+35>: testq  %rdi, %rdi  
```  
  
The same test program works on linux with boost 1.63.0 and macOS with boost 1.62.0.  
  
I didn't look deeper into it, but based on the debugger output it seems to be rather a problem of boost::context than of boost::fiber, therefore I postet it here.

---

## Comment 1

> Username: olk  
> Created at: 2016-12-30 08:44:30 UTC  
> Url: https://github.com/boostorg/context/issues/38#issuecomment-269746581  

Probably a problem in jump_fcontext assembler - MacOS uses calling  
convention of SYSV ABI so it should be equivalent to the code working on  
Linux.  
Unfortunately, I don't own a MacOS so I can't debug the code.

---

## Comment 2

> Username: tsandmann  
> Created at: 2016-12-31 15:06:22 UTC  
> Url: https://github.com/boostorg/context/issues/38#issuecomment-269868811  

I found out some more details:  
  
Using a debug build of the library, the cause of the crash seems to be parameter `dp` which is 0:  
```  
(lldb) r  
Process 72262 launched: '/Users/ts/src/fibertest/main' (x86_64)  
main()  
Process 72262 stopped  
* thread #1: tid = 0x3b9597, 0x0000000100004226 main`boost::context::execution_context<boost::fibers::detail::data_t*> boost::fibers::context::run_<main::$_0, std::__1::tuple<> >(this=0x00000001004ffec0, ctx=(fctx_ = 0x00007fff5fbfee98), fn_=0x00000001004ffea0, tpl_=0x00000001004ffea1, dp=0x0000000000000000) + 54 at context.hpp:203, queue = 'com.apple.main-thread', stop reason = EXC_BAD_ACCESS (code=1, address=0x10)  
	frame #0: 0x0000000100004226 main`boost::context::execution_context<boost::fibers::detail::data_t*> boost::fibers::context::run_<main::$_0, std::__1::tuple<> >(this=0x00000001004ffec0, ctx=(fctx_ = 0x00007fff5fbfee98), fn_=0x00000001004ffea0, tpl_=0x00000001004ffea1, dp=0x0000000000000000) + 54 at context.hpp:203  
   200 	            typename std::decay< Fn >::type fn = std::forward< Fn >( fn_);  
   201 	            typename std::decay< Tpl >::type tpl = std::forward< Tpl >( tpl_);  
   202 	            // update execution_context of calling fiber  
-> 203 	            dp->from->ctx_ = std::move( ctx);  
   204 	            if ( nullptr != dp->lk) {  
   205 	                dp->lk->unlock();  
   206 	            } else if ( nullptr != dp->ctx) {  
(lldb) bt  
* thread #1: tid = 0x3b9597, 0x0000000100004226 main`boost::context::execution_context<boost::fibers::detail::data_t*> boost::fibers::context::run_<main::$_0, std::__1::tuple<> >(this=0x00000001004ffec0, ctx=(fctx_ = 0x00007fff5fbfee98), fn_=0x00000001004ffea0, tpl_=0x00000001004ffea1, dp=0x0000000000000000) + 54 at context.hpp:203, queue = 'com.apple.main-thread', stop reason = EXC_BAD_ACCESS (code=1, address=0x10)  
  * frame #0: 0x0000000100004226 main`boost::context::execution_context<boost::fibers::detail::data_t*> boost::fibers::context::run_<main::$_0, std::__1::tuple<> >(this=0x00000001004ffec0, ctx=(fctx_ = 0x00007fff5fbfee98), fn_=0x00000001004ffea0, tpl_=0x00000001004ffea1, dp=0x0000000000000000) + 54 at context.hpp:203  
	frame #1: 0x00000001000041d6 main`boost::fibers::context::context<boost::context::basic_fixedsize_stack<boost::context::stack_traits>, main::$_0, std::__1::tuple<> >(this=0x00000001004ffe98, ctx=<unavailable>, dp=0x0000000000000000)::'lambda'(boost::context::execution_context<boost::fibers::detail::data_t*>, boost::fibers::detail::data_t*)::operator()(boost::context::execution_context<boost::fibers::detail::data_t*>, boost::fibers::detail::data_t*) + 118 at context.hpp:349  
	frame #2: 0x000000010000414a main`boost::context::detail::invoke<boost::fibers::context::context<boost::context::basic_fixedsize_stack<boost::context::stack_traits>, main::$_0, std::__1::tuple<> >(boost::fibers::worker_context_t, boost::fibers::launch, boost::context::preallocated, boost::context::basic_fixedsize_stack<boost::context::stack_traits>, main::$_0&&, std::__1::tuple<>&&)::'lambda'(boost::context::execution_context<boost::fibers::detail::data_t*>, boost::fibers::detail::data_t*), boost::context::execution_context<boost::fibers::detail::data_t*>, boost::fibers::detail::data_t*>(fn=0x00000001004ffe98, args=0x00000001004ff910, args=0x00000001004ff900), std::__1::result_of<boost::context::basic_fixedsize_stack<boost::context::stack_traits>&& (boost::context::execution_context<boost::fibers::detail::data_t*>&&, boost::fibers::detail::data_t*&&)>::type>::type, boost::context::basic_fixedsize_stack<boost::context::stack_traits>&&, boost::context::execution_context<boost::fibers::detail::data_t*>&&, boost::fibers::detail::data_t*&&) + 106 at invoke.hpp:44  
	frame #3: 0x00000001000040c7 main`boost::context::detail::apply_impl<boost::fibers::context::context<boost::context::basic_fixedsize_stack<boost::context::stack_traits>, main::$_0, std::__1::tuple<> >(boost::fibers::worker_context_t, boost::fibers::launch, boost::context::preallocated, boost::context::basic_fixedsize_stack<boost::context::stack_traits>, main::$_0&&, std::__1::tuple<>&&)::'lambda'(boost::context::execution_context<boost::fibers::detail::data_t*>, boost::fibers::detail::data_t*), std::__1::tuple<boost::context::execution_context<boost::fibers::detail::data_t*>&&, boost::fibers::detail::data_t*>, 0ul, 1ul>(fn=0x00000001004ffe98, tpl=0x00000001004ff8f8, (null)=index_sequence<0, 1> @ 0x00000001004ff7f0)std::get<0ul, 1ul>(std::forward<main::$_0>(fp0)))), boost::context::basic_fixedsize_stack<boost::context::stack_traits>&&, main::$_0&&, boost::context::detail::index_sequence<0ul, 1ul>) + 103 at apply.hpp:34  
	frame #4: 0x0000000100004054 main`_ZN5boost7context6detail5applyIZNS_6fibers7contextC1INS0_21basic_fixedsize_stackINS0_12stack_traitsEEEZ4mainE3$_0NSt3__15tupleIJEEEEENS3_16worker_context_tENS3_6launchENS0_12preallocatedET_OT0_OT1_EUlNS0_17execution_contextIJPNS3_6detail6data_tEEEESO_E_NSB_IJOSP_SO_EEEEEDTcl10apply_implclsr3stdE7forwardISG_Efp_Eclsr3stdE7forwardISH_Efp0_EcvNS1_19make_index_sequenceIXsr3std10tuple_sizeINSA_5decayISH_E4typeEEE5valueEEEilEEEOSG_SI_(fn=0x00000001004ffe98, tpl=0x00000001004ff8f8) + 52 at apply.hpp:44  
	frame #5: 0x0000000100003f10 main`boost::context::detail::record<boost::context::execution_context<boost::fibers::detail::data_t*>, boost::context::basic_fixedsize_stack<boost::context::stack_traits>, boost::fibers::context::context<boost::context::basic_fixedsize_stack<boost::context::stack_traits>, main::$_0, std::__1::tuple<> >(boost::fibers::worker_context_t, boost::fibers::launch, boost::context::preallocated, boost::context::basic_fixedsize_stack<boost::context::stack_traits>, main::$_0&&, std::__1::tuple<>&&)::'lambda'(boost::context::execution_context<boost::fibers::detail::data_t*>, boost::fibers::detail::data_t*)>::run(this=0x00000001004ffe80, t=(fctx = 0x00007fff5fbfee98, data = 0x00007fff5fbfefd0)) + 1504 at execution_context_v2.hpp:141  
	frame #6: 0x0000000100003792 main`_ZN5boost7context6detail13context_entryINS1_6recordINS0_17execution_contextIJPNS_6fibers6detail6data_tEEEENS0_21basic_fixedsize_stackINS0_12stack_traitsEEEZNS5_7contextC1ISC_Z4mainE3$_0NSt3__15tupleIJEEEEENS5_16worker_context_tENS5_6launchENS0_12preallocatedET_OT0_OT1_EUlS9_S8_E_JEEEEEvNS1_10transfer_tE(t_=(fctx = 0x00007fff5fbff1a8, data = 0x00000001004ffe80)) + 194 at execution_context_v2.hpp:70  
	frame #7: 0x0000000100111324 libboost_context-mt-d.dylib`make_fcontext + 36 at make_x86_64_sysv_macho_gas.S:51  
```  
Creating (and swichting to) the main- and dispatcher-contexts works correctly (at least no crash), so things go wrong not before the actual start of fiber f.  
  
The lasted commit working on macOS is da5208ac528ce34874a1476ca3973d85f11cde22, the commits 1aa4e6a320e0530956bd724947ef08f6444460f9 / 3feb5330e393534e919f4957a871967514f687dd lead to the crash.   
  
There are two differences in the assembler code between the linux and mac version:  
1. make_fcontext: address shift is done with 2 instructions (`movabs  $-16, %r8  \n  andq %r8, %rax`) instead of 1. That's not the problem, but is there any reason for that?  
1. The alignment (`.align 8`) for all functions is 8 instead of 16 for the linux version, what's the reason for it?  
  
JFYI: At this code in make_fcontext:  
```  
    /* third arg of make_fcontext() == address of context-function */  
    /* stored in RBX */  
    movq  %rdx, 0x28(%rax)  
```  
the comment is wrong, third arg is stored in RDX (not RBX), but the code is correct :)

---

## Comment 3

> Username: Orphis  
> Created at: 2017-01-01 07:48:23 UTC  
> Url: https://github.com/boostorg/context/issues/38#issuecomment-269894978  

Not sure if it's related but the unit tests are failing with 1.63 on macOS with Xcode 8, in C++11 mode:  
```Running 13 test cases...  
libs/context/test/test_execution_context_v2.impl:321: error: in "Boost.Context: execution_context v2 test suite/_test_ontop": check j == -70 has failed [0 != -70]  
libs/context/test/test_execution_context_v2.impl:332: error: in "Boost.Context: execution_context v2 test suite/_test_ontop": check i == 2 has failed [0 != 2]  
libs/context/test/test_execution_context_v2.impl:333: error: in "Boost.Context: execution_context v2 test suite/_test_ontop": check j == 4 has failed [0 != 4]  
unknown location:0: fatal error: in "Boost.Context: execution_context v2 test suite/_test_ontop": memory access violation at address: 0xfffffffeffffff88: no mapping at fault address  
libs/context/test/test_execution_context_v2.impl:340: last checkpoint  
  
*** 4 failures are detected in the test module "Master Test Suite"```

---

## Comment 4

> Username: Orphis  
> Created at: 2017-01-01 07:49:50 UTC  
> Url: https://github.com/boostorg/context/issues/38#issuecomment-269894995  

And it looks like the only test runner is using Xcode 7, which may not be hitting the requirements for the tests in C++11 mode (which is odd), thus it hasn't been running them at all...

---

## Comment 5

> Username: Orphis  
> Created at: 2017-01-09 20:54:00 UTC  
> Url: https://github.com/boostorg/context/issues/38#issuecomment-271404360  

I confirm everything is working fine, all context tests and fiber tests on macOS.  
But the test agents aren't running them since they're using an older version of Xcode. Do you know how to contact the owner of the macOS machines and get them to upgrade to Xcode 8 maybe?

---

## Comment 6

> Username: olk  
> Created at: 2017-01-10 08:09:14 UTC  
> Url: https://github.com/boostorg/context/issues/38#issuecomment-271511430  

Danke Timo!

---

## Comment 7

> Username: olk  
> Created at: 2017-01-10 08:11:19 UTC  
> Url: https://github.com/boostorg/context/issues/38#issuecomment-271511773  

> Do you know how to contact the owner of the macOS machines and get them to upgrade to Xcode 8 maybe?  
  
unfortunately no
