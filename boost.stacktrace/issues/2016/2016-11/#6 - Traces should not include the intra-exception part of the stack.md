# #6 - Traces should not include the intra-exception part of the stack [Closed]

> Username: eyalroz  
> Created at: 2016-11-22 11:24:05 UTC  
> Updated at: 2016-12-10 22:51:23 UTC  
> Closed at: 2016-12-10 09:54:11 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/6  

When I build and run `throwing_st.cpp` (using libunwind), it yields the following output (only including the first few lines):  
```  
Backtrace:  
 0# traced::traced() +0x28  
 1# with_trace<std::logic_error>::with_trace<char const (&) [44]>(char const (&) [44]) +0x3E  
 2# oops(int) +0x60  
 3# bar(int) +0x73  
-- etc. etc.  --  
```  
I believe that the first two lines should not be part of the stack trace; it should, IMO, start with the point of the throw, and not follow the implementation of the exception.

---

## Comment 1

> Username: apolukhin  
> Created at: 2016-12-10 09:54:11 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/6#issuecomment-266194708  

This is not possible. Function inlinements depend on a compiler options, compiler version and function usage. There's no portable way to predict inlinement.  
  
Compilers may ignore inline/noinline/forceinline attributes, so it is impossible to provide portable behavior using those attributes.

---

## Comment 2

> Username: eyalroz  
> Created at: 2016-12-10 10:11:12 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/6#issuecomment-266197356  

This actually is possible, at the very least by looking at the trace. If you find "with_trace<..." as the first or second (can't be the third I guess) record in the backtrace, clip it.  
  
Also, does it actually happen that inlining makes that part of the stack trace disappear?

---

## Comment 3

> Username: apolukhin  
> Created at: 2016-12-10 22:51:23 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/6#issuecomment-266245525  

Sounds like too many troubles for just **an example**.  
  
No, inlining is just a recommendation for a compilers. They sometimes ignore even the forceinline attribute and produce a warning on that.
