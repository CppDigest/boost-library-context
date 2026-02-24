# #65 - `__sanitizer_finish_switch_fiber` should be called directly after context switch [Closed]

> Username: jspam  
> Created at: 2017-09-01 07:13:34 UTC  
> Updated at: 2017-09-26 13:07:39 UTC  
> Closed at: 2017-09-26 13:07:39 UTC  
> Url: https://github.com/boostorg/context/issues/65  

According to the [AddressSanitizer API](https://code.woboq.org/gcc/libsanitizer/include/sanitizer/common_interface_defs.h.html#162):  
  
> When code starts running on the new stack, it must call `__sanitizer_finish_switch_fiber` to finalize the switch.  
  
Currently, Boost.Context calls `__sanitizer_finish_switch_fiber` directly *before* the context switch (instead of direc;tly *after*):  
  
https://github.com/boostorg/context/blob/82bf54e9c2e4d21f864b6471ed3b087099d091e3/include/boost/context/continuation_ucontext.hpp#L144  
  
This basically leads to ASan permanently being in "switching stacks" mode. Consequently, the test program from https://github.com/boostorg/coroutine/issues/30#issuecomment-325574878 crashes with an AddressSanitizer error even when using Boost.Coroutine2.

---

## Comment 1

> Username: olk  
> Created at: 2017-09-08 09:27:22 UTC  
> Updated at: 2017-09-08 09:28:10 UTC  
> Url: https://github.com/boostorg/context/issues/65#issuecomment-328053002  

Fix should work (tested with example segmented.cpp -> stack exhaustion).  
Unfortunately ASan complains sometimes that special address ranges are already used/allocated and quits:  
'Shadow memory range interleaves with an existing memory mapping. ASan cannot proceed correctly. ABORTING.'
