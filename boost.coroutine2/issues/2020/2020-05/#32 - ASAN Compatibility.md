# #32 - ASAN Compatibility [Closed]

> Username: eppingere  
> Created at: 2020-05-14 18:14:18 UTC  
> Updated at: 2023-10-01 15:01:32 UTC  
> Closed at: 2023-10-01 15:01:32 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/32  

Similar to [this question from coroutines](https://github.com/boostorg/coroutine/issues/39), I have run into issues with ASAN. I looked into [this previous issue](https://github.com/boostorg/coroutine2/issues/12) and was wondering if there is any update on this.  
  
I get a stack underflow when a running coroutine is deconstructed. According to the issue with coroutines, this is caused by a `detail::forced_unwind exception` that is caught but I have been able to create it without catching the exception:  
  
```  
auto p = pull_type([&](push_type &s) {   
  while (true) sink();  
});  
```  
  
This results in the following ASAN error:   
```  
==48790==ERROR: AddressSanitizer: stack-buffer-underflow on address 0x00010cc4acc0 at pc 0x000108aaaedf bp 0x00010cc4a7d0 sp 0x00010cc49f90  
WRITE of size 168 at 0x00010cc4acc0 thread T0  
    #0 0x108aaaede in wrap_memmove (libclang_rt.asan_osx_dynamic.dylib:x86_64h+0x1aede)  
  
0x00010cc4acc0 is located 128192 bytes inside of 131072-byte region [0x00010cc2b800,0x00010cc4b800)  
allocated by thread T0 here:  
    #0 0x108ad5abd in wrap_malloc (libclang_rt.asan_osx_dynamic.dylib:x86_64h+0x45abd)  
    #1 0x107cb9b3e in boost::context::basic_fixedsize_stack<boost::context::stack_traits>::allocate() fixedsize_stack.hpp:57  
    #2 0x107cb9535 in boost::coroutines2::detail::pull_coroutine<void>::control_block* boost::coroutines2::detail::create_control_block<boost::coroutines2::detail::pull_coroutine<void>::control_block, boost::context::basic_fixedsize_stack<boost::context::stack_traits>, terrier::common::PoolContext::in_::'lambda'(boost::coroutines2::detail::push_coroutine<void>&)>(boost::context::basic_fixedsize_stack<boost::context::stack_traits>&&, terrier::common::PoolContext::in_::'lambda'(boost::coroutines2::detail::push_coroutine<void>&)&&) create_control_block.ipp:32  
    #3 0x107cb92bd in boost::coroutines2::detail::pull_coroutine<void>::pull_coroutine<boost::context::basic_fixedsize_stack<boost::context::stack_traits>, terrier::common::PoolContext::in_::'lambda'(boost::coroutines2::detail::push_coroutine<void>&)>(boost::context::basic_fixedsize_stack<boost::context::stack_traits>&&, terrier::common::PoolContext::in_::'lambda'(boost::coroutines2::detail::push_coroutine<void>&)&&) pull_coroutine.ipp:184  
    #4 0x107cb9214 in boost::coroutines2::detail::pull_coroutine<void>::pull_coroutine<boost::context::basic_fixedsize_stack<boost::context::stack_traits>, terrier::common::PoolContext::in_::'lambda'(boost::coroutines2::detail::push_coroutine<void>&)>(boost::context::basic_fixedsize_stack<boost::context::stack_traits>&&, terrier::common::PoolContext::in_::'lambda'(boost::coroutines2::detail::push_coroutine<void>&)&&) pull_coroutine.ipp:184  
    #5 0x107cb9099 in boost::coroutines2::detail::pull_coroutine<void>::pull_coroutine<terrier::common::PoolContext::in_::'lambda'(boost::coroutines2::detail::push_coroutine<void>&), void>(terrier::common::PoolContext::in_::'lambda'(boost::coroutines2::detail::push_coroutine<void>&)&&) pull_coroutine.ipp:179  
```  
  
Is there a workaround to resolve this ASAN issue?

---

## Comment 1

> Username: zeyangl  
> Created at: 2021-05-21 02:59:57 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/32#issuecomment-845616506  

Seeing the same issue. Really annoying as this throws the whole address sanitizer out of the window, when it's actually pretty useful.

---

## Comment 2

> Username: olk  
> Created at: 2023-10-01 15:01:32 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/32#issuecomment-1742108301  

unfortunately no
