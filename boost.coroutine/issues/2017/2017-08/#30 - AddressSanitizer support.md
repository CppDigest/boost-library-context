# #30 - AddressSanitizer support [Closed]

> Username: vinniefalco  
> Created at: 2017-08-04 15:00:46 UTC  
> Updated at: 2017-08-29 07:48:20 UTC  
> Closed at: 2017-08-04 15:40:33 UTC  
> Url: https://github.com/boostorg/coroutine/issues/30  

Can we please add something like `<asan>on` (similar to `<valgrind>on`) which informs AddressSanitizer of the stack changes? It is described here:  
https://github.com/google/sanitizers/issues/189#issuecomment-312914329  
https://github.com/llvm-mirror/compiler-rt/blob/master/include/sanitizer/common_interface_defs.h#L166

---

## Comment 1

> Username: olk  
> Created at: 2017-08-04 15:40:33 UTC  
> Url: https://github.com/boostorg/coroutine/issues/30#issuecomment-320281667  

already contained in boost.context (boost-1.65 release) -> BOOST_USE_ASAN  
but without bjam property (will be obsolete after transission to cmake)

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-08-04 15:46:47 UTC  
> Url: https://github.com/boostorg/coroutine/issues/30#issuecomment-320283145  

Awesome!!!

---

## Comment 3

> Username: jspam  
> Created at: 2017-08-24 14:03:25 UTC  
> Url: https://github.com/boostorg/coroutine/issues/30#issuecomment-324644527  

I tried this, but noted that `BOOST_USE_ASAN` is used only in `boost/context/continuation_ucontext.hpp` and should therefore only be available when Boost is compiled with `context-impl=ucontext` (as opposed to `fcontext`).  
  
However, Boost.Coroutine includes `boost/context/detail/fcontext.hpp` directly, which implies that it uses some `fcontext` functions and cannot fully make use of `BOOST_USE_ASAN`.  
  
Indeed, using `BOOST_USE_ASAN` I still get an ASan warning about false positive error reports followed by an ASan error whose stack trace begins with `make_fcontext` (see below).  
  
Can I assume that to get AddressSanitizer support I need to switch to Boost.Coroutine2? Or did I miss something?  
  
```  
==20341==WARNING: ASan is ignoring requested __asan_handle_no_return: stack top: 0x7ffffffff000; bottom 0x7fffeb7a8000; size: 0x000014857000 (344289280)  
False positive error reports may follow  
For details see http://code.google.com/p/address-sanitizer/issues/detail?id=189  
=================================================================  
==20341==ERROR: AddressSanitizer: stack-buffer-overflow on address 0x7fffeb7a9820 at pc 0x0000004477eb bp 0x7fffeb7a9650 sp 0x7fffeb7a9648  
WRITE of size 4 at 0x7fffeb7a9820 thread T0  
[... omitted ...]  
    #17 0x454d45 in void boost::coroutines::detail::trampoline_void<boost::coroutines::detail::symmetric_coroutine_impl<void> >(boost::context::detail::transfer_t) /usr/local/include/boost/coroutine/detail/trampoline.hpp:60  
    #18 0x7fffef797fae in make_fcontext (/usr/local/lib/libboost_context.so.1.65.0+0x2fae)  
```

---

## Comment 4

> Username: olk  
> Created at: 2017-08-24 14:06:15 UTC  
> Updated at: 2017-08-24 14:06:33 UTC  
> Url: https://github.com/boostorg/coroutine/issues/30#issuecomment-324645321  

that's because the generated instrumented code needs more stack - the error should be vanish if you use a large stack (at least that was the case in my tests)

---

## Comment 5

> Username: olk  
> Created at: 2017-08-24 15:41:59 UTC  
> Url: https://github.com/boostorg/coroutine/issues/30#issuecomment-324674276  

Unfortunately, some fixes did not permission to merged into 1.65 (no permission because too late) - could you could check branch develop please?

---

## Comment 6

> Username: jspam  
> Created at: 2017-08-29 07:08:34 UTC  
> Updated at: 2017-08-29 07:18:18 UTC  
> Url: https://github.com/boostorg/coroutine/issues/30#issuecomment-325574878  

The only fix I saw is cbab418fa62a175878f4d105a2dd4c21cbcceef4 (correct?), and I could reproduce the problem on branch develop as well.  
  
Here is a simple test case:  
```  
#include <exception>  
#include <iostream>  
#include <boost/coroutine/coroutine.hpp>  
  
void throw_something() {  
  // The stack frame of this function is not cleaned up after the throw().  
  // [ASan is ignoring requested __asan_handle_no_return]  
  // Make sure we have something to not clean up.  
  int a[10];  
  a[0] = 0;  
  std::cout << a[0];  
  
  throw std::runtime_error("hello world");  
}  
  
// The calling sequence looks as follows:  
// > my_coroutine  
// >> throw_something() -- stack frame not cleaned up  
// >> write_to_stack  
//  
// If write_to_stack's stack frame is bigger than that of throw_something,  
// it will contain the (still poisoned) stack frame of throw_something.  
void write_to_stack() {  
  constexpr size_t kArraySize = 10000;  
  int a[kArraySize];  
  for (size_t i = 0; i < kArraySize; i++) {  
    a[i] = i;  
  }  
  std::cout << a[0];  
}  
  
void my_coroutine(boost::coroutines::symmetric_coroutine<void>::yield_type& yield) {  
  try {  
    throw_something();  
  } catch(...) {  
    write_to_stack();  
  }  
}  
  
int main() {  
  boost::coroutines::symmetric_coroutine<void>::call_type coroutine(my_coroutine);  
  coroutine();  
  return 0;  
}  
```  
  
Compiled with `$ g++ -Wall -Werror -std=c++11 -fsanitize=address -DBOOST_USE_ASAN -DBOOST_COROUTINES_NO_DEPRECATION_WARNING -lboost_system -lboost_coroutine -o test_coroutine2_asan -O0 -gdwarf -fno-omit-frame-pointer -fno-rtti  test_coroutine2.cpp` on both g++ 6.3.0 and g++ 7.1.1 (edit: reproducible with clang 4.0.1 as well when changing the array size in `throw_something` to 80):  
  
```  
$ ./test_coroutine2_asan   
==9312==WARNING: ASan is ignoring requested __asan_handle_no_return: stack top: 0x7ffffffff000; bottom 0x631000023000; size: 0x1ceffffdc000 (31817117581312)  
False positive error reports may follow  
For details see http://code.google.com/p/address-sanitizer/issues/detail?id=189  
=================================================================  
==9312==ERROR: AddressSanitizer: stack-buffer-underflow on address 0x631000024590 at pc 0x0000004082fe bp 0x63100001a960 sp 0x63100001a958  
WRITE of size 4 at 0x631000024590 thread T0  
    #0 0x4082fd in write_to_stack() /home/user/test_coroutine/test_coroutine2.cpp:27  
    #1 0x40838e in my_coroutine(boost::coroutines::detail::symmetric_coroutine_yield<void>&) /home/user/test_coroutine/test_coroutine2.cpp:36  
    #2 0x40dede in boost::coroutines::detail::symmetric_coroutine_object<void, void (&)(boost::coroutines::detail::symmetric_coroutine_yield<void>&), boost::coroutines::basic_standard_stack_allocator<boost::coroutines::stack_traits> >::run() /usr/local/include/boost/coroutine/detail/symmetric_coroutine_object.hpp:241  
    #3 0x40c459 in void boost::coroutines::detail::trampoline_void<boost::coroutines::detail::symmetric_coroutine_impl<void> >(boost::context::detail::transfer_t) /usr/local/include/boost/coroutine/detail/trampoline.hpp:60  
    #4 0x7ffff5423fbe in make_fcontext (/usr/local/lib/libboost_context.so.1.66.0+0x2fbe)  
  
0x631000024590 is located 64912 bytes inside of 65536-byte region [0x631000014800,0x631000024800)  
allocated by thread T0 here:  
    #0 0x7ffff6effad8 in malloc (/usr/local/lib64/libasan.so.3+0xc1ad8)  
    #1 0x40d0c5 in boost::coroutines::basic_standard_stack_allocator<boost::coroutines::stack_traits>::allocate(boost::coroutines::stack_context&, unsigned long) /usr/local/include/boost/coroutine/standard_stack_allocator.hpp:42  
    #2 0x40c574 in boost::coroutines::detail::symmetric_coroutine_call<void>::symmetric_coroutine_call<void (&)(boost::coroutines::detail::symmetric_coroutine_yield<void>&)>(void (&)(boost::coroutines::detail::symmetric_coroutine_yield<void>&), boost::coroutines::attributes const&, boost::coroutines::basic_standard_stack_allocator<boost::coroutines::stack_traits>) /usr/local/include/boost/coroutine/detail/symmetric_coroutine_call.hpp:627  
    #3 0x40845e in main /home/user/test_coroutine/test_coroutine2.cpp:41  
    #4 0x7ffff5e7d2e0 in __libc_start_main (/lib/x86_64-linux-gnu/libc.so.6+0x202e0)  
  
SUMMARY: AddressSanitizer: stack-buffer-underflow /home/user/test_coroutine/test_coroutine2.cpp:27 in write_to_stack()  
Shadow bytes around the buggy address:  
  0x0c627fffc860: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
  0x0c627fffc870: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
  0x0c627fffc880: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
  0x0c627fffc890: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
  0x0c627fffc8a0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
=>0x0c627fffc8b0: 00 00[f1]f1 f1 f1 00 00 00 00 00 f4 f3 f3 f3 f3  
  0x0c627fffc8c0: f3 f3 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
  0x0c627fffc8d0: f1 f1 f1 f1 00 f4 f4 f4 f2 f2 f2 f2 00 00 f4 f4  
  0x0c627fffc8e0: f3 f3 f3 f3 00 00 00 00 00 00 00 00 00 00 00 00  
  0x0c627fffc8f0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  
  0x0c627fffc900: fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa  
```  
  
What happens is that ASan is not notified about the stack switch and therefore refuses to clean up the stack frame of `throw_something` (because stack top and bottom differ too much, see [here](https://github.com/clang-omp/compiler-rt_trunk/blob/93a50dbdb518d19d5120af03a8479d42ed951c48/lib/asan/asan_rtl.cc#L562)). The writes in `write_to_stack` then hit the poisoned region and cause the crash.  
  
`BOOST_USE_ASAN` should enable notifying ASan about stack switches, but again it only seems to work when using ucontext.

---

## Comment 7

> Username: olk  
> Created at: 2017-08-29 07:25:39 UTC  
> Url: https://github.com/boostorg/coroutine/issues/30#issuecomment-325578344  

Yes, ASAN is only supported for ucontext (see continuation_ucontext.hpp) - that's because the assembler implementation (continuation_fcontext.hpp) does not use global pointers, so it has no way to store restore stack bottoms.

---

## Comment 8

> Username: jspam  
> Created at: 2017-08-29 07:43:17 UTC  
> Url: https://github.com/boostorg/coroutine/issues/30#issuecomment-325581979  

Thanks for the confirmation. It looks like Boost.Coroutine is using some `fcontext` functionality directly (see [here](https://github.com/boostorg/coroutine/blob/73fbcaf93d132057353518c4feedde0aee3a1801/src/detail/coroutine_context.cpp#L41)), without the library user being given a choice to use `ucontext` instead. This would mean Boost.Coroutine cannot be used with ASan at the moment, correct?

---

## Comment 9

> Username: olk  
> Created at: 2017-08-29 07:48:20 UTC  
> Url: https://github.com/boostorg/coroutine/issues/30#issuecomment-325583085  

Boost.Coroutine is deprecated - so it will never benefit from new features.  
You could use Boost.Coroutine2 instead in order to get ASAN working (re-build with context-impl=ucontext + BOOST_USE_ASAN).
