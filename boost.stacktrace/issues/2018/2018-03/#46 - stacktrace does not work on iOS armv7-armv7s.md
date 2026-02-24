# #46 - stacktrace does not work on iOS armv7/armv7s [Closed]

> Username: theodelrieu  
> Created at: 2018-03-08 17:13:50 UTC  
> Updated at: 2020-01-25 14:09:31 UTC  
> Closed at: 2020-01-25 14:09:31 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/46  

Hello,  
  
When attempting to use the header only version of Stacktrace, I get the following linker error:  
  
>Undefined symbols for architecture armv7:  
  "__Unwind_Backtrace", referenced from:  
      boost::stacktrace::detail::this_thread_frames::collect(void const**, unsigned long, unsigned long) in libmylib.a(Error.cpp.o)  
ld: symbol(s) not found for architecture armv7  
  
I did look in the `libSystem.tbd` and the `__Unwind_Backtrace` symbol is only defined for arm64.  
  
The workaround is to define the `BOOST_STACKTRACE_USE_NOOP` macro. Is there another way to workaround the problem?

---

## Comment 1

> Username: apolukhin  
> Created at: 2018-03-10 06:28:10 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/46#issuecomment-372007184  

Did you link with libbacktrace and standard library?  
Could you please provide a compile and link commands that trigger the issue?

---

## Comment 2

> Username: theodelrieu  
> Created at: 2018-03-12 13:52:16 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/46#issuecomment-372316460  

I tried to find a way to get libbacktrace for iOS (and Darwin platforms in general), but it seems upstream was not updated for a while, and the only recent patches I've seen are from a fork inside rustlang.  
  
Do you know if there is a way to get libbacktrace working on macOS/iOS?

---

## Comment 3

> Username: ivanarh  
> Created at: 2018-07-02 23:43:49 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/46#issuecomment-401969913  

I've already faced this problem. A solution is to use backtrace() function from execinfo.h instead of __Unwind_Backtrace.

---

## Comment 4

> Username: apolukhin  
> Created at: 2020-01-25 14:09:31 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/46#issuecomment-578409269  

This should have been fixed in #70   
Please reopen if #70 does not solve issue for you.   
  
Many thanks for the report!
