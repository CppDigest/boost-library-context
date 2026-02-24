# #14 Fix for BOOST_NO_CXX11_RVALUE_REFERENCES [Merged]

> Username: MarcelRaad  
> Created at: 2014-06-12 15:42:39 UTC  
> Updated at: 2014-06-13 19:21:30 UTC  
> Merged at: 2014-06-12 16:40:01 UTC  
> Closed at: 2014-06-12 16:40:01 UTC  
> Url: https://github.com/boostorg/thread/pull/14  

Fix compilation of future.hpp with BOOST_NO_CXX11_RVALUE_REFERENCES defined. Tested on clang and MSVC12 with and without BOOST_NO_CXX11_RVALUE_REFERENCES defined.  
  
BOOST_NO_CXX11_RVALUE_REFERENCES has to be defined if the compiler provides rvalue references, but the standard library doesn't provide std::move. Without these fixes, the deleted copy constructor is called.

---

## Comment 1

> Username: viboes  
> Created_at: 2014-06-12 17:46:18 UTC  
> Url: https://github.com/boostorg/thread/pull/14#issuecomment-45924442  

Unfortunately the pull doesn't works with clang. I'll take a look.

---

## Comment 2

> Username: MarcelRaad  
> Created_at: 2014-06-12 18:02:23 UTC  
> Url: https://github.com/boostorg/thread/pull/14#issuecomment-45926475  

Oh, I'm very sorry! Obviously I haven't tested all combinations of BOOST_NO_CXX11_RVALUE_REFERENCES and C++03/11 :-(  
Visual C++ 2013 definitely works with BOOST_NO_CXX11_RVALUE_REFERENCES if BOOST_THREAD_DONT_PROVIDE_FUTURE_UNWRAP is also defined, and without BOOST_NO_CXX11_RVALUE_REFERENCES the change is a no-op.

---

## Comment 3

> Username: MarcelRaad  
> Created_at: 2014-06-13 07:34:56 UTC  
> Url: https://github.com/boostorg/thread/pull/14#issuecomment-45983564  

Ah, seems like it works with BOOST_THREAD_USES_MOVE on clang (BOOST_THREAD_VERSION >= 3) but doesn't work without it.

---

## Comment 4

> Username: viboes  
> Created_at: 2014-06-13 16:15:38 UTC  
> Url: https://github.com/boostorg/thread/pull/14#issuecomment-46030342  

Which version are you using?

---

## Comment 5

> Username: MarcelRaad  
> Created_at: 2014-06-13 19:21:30 UTC  
> Url: https://github.com/boostorg/thread/pull/14#issuecomment-46050187  

I'm using version 4.

---
