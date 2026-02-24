# #12 - AddressSanitizer support [Closed]

> Username: vinniefalco  
> Created at: 2017-08-04 15:01:00 UTC  
> Updated at: 2018-10-24 06:39:57 UTC  
> Closed at: 2017-08-04 16:00:06 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/12  

Can we please add something like `<asan>on` (similar to `<valgrind>on`) which informs AddressSanitizer of the stack changes? It is described here:  
https://github.com/google/sanitizers/issues/189#issuecomment-312914329  
https://github.com/llvm-mirror/compiler-rt/blob/master/include/sanitizer/common_interface_defs.h#L166

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-08-04 15:47:02 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/12#issuecomment-320283199  

Does `BOOST_USE_ASAN` work?

---

## Comment 2

> Username: olk  
> Created at: 2017-08-04 16:00:06 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/12#issuecomment-320286447  

I think yes, but you need a very up-to-date version of libasan.  
It is only applicable to ucontext (don't know if ASAN is supported on windows - especially the __sanitizer_start_switch_fiber etc.). You can not use it for the assembler backend because this code has no access to the fake-stack, stack-bottom etc. - no global pointer as in ucontext backend.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-08-04 16:02:33 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/12#issuecomment-320287108  

That's cool, I just want to get rid of the false positives. I only run ubsan and asan as part of the CI tests on Travis and they make heavy use of Asio coroutines:  
https://travis-ci.org/vinniefalco/beast/jobs/260836700#L1417  
  
So I think this will work well, thanks!

---

## Comment 4

> Username: bog-dan-ro  
> Created at: 2018-10-23 18:21:20 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/12#issuecomment-432362842  

Do you have more info on this matter? E.g. what is the minimum boost, asan & gcc versions?  
I tried Debian's testing boost 1.67 and gcc 8 and the false positive is still there...

---

## Comment 5

> Username: olk  
> Created at: 2018-10-24 06:27:23 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/12#issuecomment-432529058  

I'm sorry, no - no new infos

---

## Comment 6

> Username: hamparawa  
> Created at: 2018-10-24 06:39:57 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/12#issuecomment-432531756  

I've also experienced issues with GCC as well. Therefore I had to switched to Clang latest only for CI builds.
