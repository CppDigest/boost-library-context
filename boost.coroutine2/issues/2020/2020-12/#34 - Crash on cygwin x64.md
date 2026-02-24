# #34 - Crash on cygwin x64 [Closed]

> Username: viric  
> Created at: 2020-12-15 23:08:43 UTC  
> Updated at: 2020-12-17 07:26:22 UTC  
> Closed at: 2020-12-17 07:26:22 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/34  

Coroutine2 does not seem to work under cygwin.  
  
This code:  
https://github.com/NixOS/nix/blob/2.3.9/src/libutil/serialise.cc#L188  
crashes right away with a quite misterious backtrace on Access Violation.  
  
I don't know whether this is a problem of coroutine2 or context. https://github.com/boostorg/context

---

## Comment 1

> Username: olk  
> Created at: 2020-12-16 07:15:17 UTC  
> Updated at: 2020-12-16 07:16:53 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/34#issuecomment-745815672  

unfortunately cygwin is not supported - msvc (masm), clang  or mingw work

---

## Comment 2

> Username: viric  
> Created at: 2020-12-16 08:12:50 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/34#issuecomment-745887044  

What if we provide a patch that would make it work? Or it will be refused?

---

## Comment 3

> Username: olk  
> Created at: 2020-12-16 08:36:44 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/34#issuecomment-745917387  

you are welcome to provide a patch/pull-request

---

## Comment 4

> Username: viric  
> Created at: 2020-12-16 18:16:41 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/34#issuecomment-746762673  

Hmmm I found a workaround. This made it work:  
https://www.boost.org/doc/libs/1_75_0/libs/context/doc/html/context/architectures.html  
  
" If the architecture is not supported but the platform provides ucontext_t, Boost.Context should be compiled with BOOST_USE_UCONTEXT and b2 property context-impl=ucontext. "  
  
Could we get in a patch that, on cygwin, chooses that configuration? Or that would be the first ever about cygwin?
