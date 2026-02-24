# #323 - Exception state can be saved to incorrect thread in 1.88 [Open]

> Username: zhykzhykzhyk  
> Created at: 2025-12-25 17:00:36 UTC  
> Updated at: 2025-12-25 17:00:36 UTC  
> Url: https://github.com/boostorg/context/issues/323  

The `manage_exception_state` try to save and restore the exception state from/to the current thread that executing the fiber, but only one `__cxa_get_globals()` call is generated for the outer function after optimization, which would be incorrect if fiber resume on another thread.  
  
https://github.com/boostorg/context/blob/6ff80e0575133c6d0b704e03bbb956a0c3b9551a/include/boost/context/fiber_fcontext.hpp#L88-L98  
  
[Result from compiler explorer](https://godbolt.org/z/796Ksjded)  
```C++  
// check disassembly of this function  
void foo(boost::context::fiber&& f) { std::move(f).resume(); }  
```
