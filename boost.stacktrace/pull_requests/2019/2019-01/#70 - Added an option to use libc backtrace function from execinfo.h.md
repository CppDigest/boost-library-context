# #70 Added an option to use libc backtrace function from execinfo.h [Merged]

> Username: ivanarh  
> Created at: 2019-01-10 17:13:29 UTC  
> Updated at: 2019-01-12 19:38:09 UTC  
> Merged at: 2019-01-11 18:59:45 UTC  
> Closed at: 2019-01-11 18:59:45 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/70  

* Added new definition BOOST_STACKTRACE_USE_LIBC_BACKTRACE_FUNCTION  
* Set on iOS 32-bit platforms. See https://github.com/boostorg/stacktrace/issues/46

---

## Comment 1

> Username: coveralls  
> Created_at: 2019-01-10 17:30:45 UTC  
> Updated_at: 2019-01-11 10:47:04 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/70#issuecomment-453183294  

[![Coverage Status](https://coveralls.io/builds/21021927/badge)](https://coveralls.io/builds/21021927)  
  
Coverage increased (+0.02%) to 90.265% when pulling **839a1a127d206e73eb24de2421fa7405971b2c68 on ivanarh:use_execinfo_backtrace** into **d708d17ecdae84344069afaf283ac3e5e54a5465 on boostorg:develop**.

---

## Comment 2

> Username: apolukhin  
> Created_at: 2019-01-11 19:01:00 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/70#issuecomment-453621957  

Many thanks for the PR!  
  
I'm worried about async signal safety of backtrace() on Ios. Are there any guarantees?

---

## Comment 3

> Username: ivanarh  
> Created_at: 2019-01-12 11:44:17 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/70#issuecomment-453741084  

> I'm worried about async signal safety of backtrace() on Ios. Are there any guarantees?  
  
It may depend on libc implementation and (or) on platform. For example, let's take a look to it's source in glibc:  
https://github.com/lattera/glibc/blob/master/debug/backtrace.c  
It's a simple wrapper around _Unwind_Backtrace. But it's loaded by dlopen/dlsym calls. So if backtrace() function is called for a first time from signal handler it's may be unsafe. But a workaround may be used: backtrace() should be called once before any signal handlers.  
  
What about apple libc implementation, it's completely different:  
https://opensource.apple.com/source/Libc/Libc-1272.200.26/gen/backtrace.c.auto.html  
https://opensource.apple.com/source/Libc/Libc-1272.200.26/gen/thread_stack_pcs.c.auto.html  
It relies on [Apple ABI](https://developer.apple.com/library/archive/documentation/Xcode/Conceptual/iPhoneOSABIReference/Articles/ARMv6FunctionCallingConventions.html#//apple_ref/doc/uid/TP40009021-SW1) where an old frame pointer is saved to the stack. All it does is jumping from current frame pointer to previous in a cycle. But couple of external calls exist:  
```  
pthread_t self = pthread_self();  
void *stacktop = pthread_get_stackaddr_np(self);  
void *stackbot = stacktop - pthread_get_stacksize_np(self);  
```  
pthread_self is signal safe, pthread_get_stackaddr_np is not properly documented.  
https://opensource.apple.com/source/libpthread/libpthread-218.60.3/src/pthread.c.auto.html  
I see some explicit locks in its source code:  
```  
void *  
pthread_get_stackaddr_np(pthread_t t)  
{  
	int ret;  
	void *addr = NULL;  
  
	if (t == NULL) {  
		return (void *)(uintptr_t)ESRCH; // XXX bug?  
	}  
	  
	// since the main thread will not get de-allocated from underneath us  
	if (t == pthread_self() || t == &_thread) {  
		return t->stackaddr;  
	}  
  
	_PTHREAD_LOCK(_pthread_list_lock);  
```  
So it may be unsafe. But this code isn't reached when we work with a current thread.  
  
Therefore, signal safety of backtrace function is an arguable question.

---

## Comment 4

> Username: apolukhin  
> Created_at: 2019-01-12 19:38:09 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/70#issuecomment-453775295  

Looks safe to me. Many thanks!

---
