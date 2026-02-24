# #99 - example yield.hpp possibly destroys locked mutex [Closed]

> Username: thadeuluiz  
> Created at: 2016-11-23 00:35:02 UTC  
> Updated at: 2017-04-30 19:18:03 UTC  
> Closed at: 2017-04-30 19:18:03 UTC  
> Url: https://github.com/boostorg/fiber/issues/99  

I posted this on the boost bugtracker however it wasnt noticed by anyone, so ill post it here.  
  
In the examples/asio/detail/yield.hpp file, the sleeping fiber owns the mutex(for when `async_result::get` returns, the asio call returns and the yield_completion is destroyed).  
Consider Fiber A, the callee of the async operation, and Fiber B, the fiber that calls async_handler.  
Fiber B possibly runs in another thread.  
  
Fiber B locks the mutex, sets fiber A as ready and goes to sleep without releasing the lock. Fiber A goes through the return process and destroys the mutex.  
  
The solution is very simple. Simply reaquire the lock after the `suspend` call returns.

---

## Comment 1

> Username: olk  
> Created at: 2016-11-23 07:08:57 UTC  
> Updated at: 2016-11-23 07:12:24 UTC  
> Url: https://github.com/boostorg/fiber/issues/99#issuecomment-262446334  

Because you did not mention the code lines I assume you refer to the implementation of yield_completion::wait().  
  
The fiber calling this function locks the mutex (that protects variable completed_) and the suspends if the work was not completed yet.  
Please note how the fiber is suspended: fibers::context::active()->suspend( lk);  
The lock is released after this fiber has been suspended.

---

## Comment 2

> Username: thadeuluiz  
> Created at: 2016-11-23 15:10:53 UTC  
> Url: https://github.com/boostorg/fiber/issues/99#issuecomment-262540661  

exactly, however, when suspend returns, it does not reaquire the lock, which is needed for safety

---

## Comment 3

> Username: olk  
> Created at: 2016-11-23 17:35:33 UTC  
> Url: https://github.com/boostorg/fiber/issues/99#issuecomment-262582063  

Why should it acquire the lock again - the variable isn't required to protected again.

---

## Comment 4

> Username: thadeuluiz  
> Created at: 2016-11-23 18:24:10 UTC  
> Updated at: 2016-11-23 18:47:28 UTC  
> Url: https://github.com/boostorg/fiber/issues/99#issuecomment-262594005  

I'll try to make it as clear as I can.  
  
The thread that calls `fibers::context::active()->set_ready( ctx_)` does so **while holding the lock**.  
  
The thread that created the lock then resumes. Note that the notifying thread might still hold the lock. It might have been suspended from execution by the OS or whatever.  
The resumed thread that created the lock continues and destroys the mutex.  
  
The notifying thread never releases the lock, yet it is destroyed.  
  
I assumed that on return of `fibers::context::active()->suspend( lk)`, lk would own the mutex, but it is not the case.  
  
this might explain what im saying.  
https://gist.github.com/thadeuluiz/d1d15d25d7875f1eab2089fedd707580

---

## Comment 5

> Username: MacoChris  
> Created at: 2017-02-17 06:23:05 UTC  
> Url: https://github.com/boostorg/fiber/issues/99#issuecomment-280564744  

Can confirm this is an issue in `yield_handler_base::operator()(error_code)` when a different thread resumes the fibre than the one waiting on it (ASAN complains about a bad read when releasing `lk`, since the resumed fibre may have already destroyed the lock).   
  
> I assumed that on return of fibers::context::active()->suspend( lk), lk would own the mutex, but it is not the case.  
  
I also assumed this, and am surprised it's not the case (especially when this function is not documented). The current fix we're trying over the weekend is to add `lk.lock();` at `detail/yield.hpp:50`.

---

## Comment 6

> Username: olk  
> Created at: 2017-02-17 06:59:33 UTC  
> Updated at: 2017-02-17 07:00:26 UTC  
> Url: https://github.com/boostorg/fiber/issues/99#issuecomment-280570569  

fibers::context is not part of public the public API, it should not be used by the users.  
keep in mind that the asio binding is an example - especially some support is required by the asio library (see #102).  
  
I'm focused on other parts of the library - so I've no time to take care for the asio binding.

---

## Comment 7

> Username: MacoChris  
> Created at: 2017-02-17 07:19:42 UTC  
> Url: https://github.com/boostorg/fiber/issues/99#issuecomment-280573750  

Yeah, we're running asio and the fibers in different threads to avoid the main-loop annoyance / integrate better with existing code.  
  
It seems weird that `yield` is example code, but relies on internal APIs. Would we be better off writing something analogous to the 'use_future' in asio, returning a fiber future if we want less risk of interface breakage?

---

## Comment 8

> Username: olk  
> Created at: 2017-04-30 19:16:11 UTC  
> Url: https://github.com/boostorg/fiber/issues/99#issuecomment-298250974  

The problem is not related to the lock - the problem is that yield_completion is shared between async_result and yield_handler. It should go out of scope if the last reference (yield_handler or async_result) goes out of scope. Using an intrusive poitner should fix the issue.
