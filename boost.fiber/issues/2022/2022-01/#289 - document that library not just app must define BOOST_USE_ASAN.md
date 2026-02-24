# #289 - document that library not just app must define BOOST_USE_ASAN [Open]

> Username: Jacob-Burckhardt  
> Created at: 2022-01-25 01:12:08 UTC  
> Updated at: 2025-11-14 15:13:15 UTC  
> Url: https://github.com/boostorg/fiber/issues/289  

The documentation says:  
  
> Support for sanitizers  
> Sanitizers (GCC/Clang) are confused by the stack switches. **The library (and Boost.Context too) is required to be compiled with property (b2 command-line) context-impl=ucontext and compilers santizer options.** _Users must define BOOST_USE_ASAN before including any Boost.Context headers when linking against Boost binaries._  
  
The italics sentence says to define BOOST_USE_ASAN.  Please make the bold sentence also say to define BOOST_USE_ASAN.     
  
Maybe some people would interpret the italics to refer to building the Fiber library but I thought it referred to building only my application which used the Fiber library since it uses terms like "users" which I think means a user of the Fiber library, not maintainers or builders of the library.    
  
The italics sentence also refers to "linking" against the binaries.  I think an application links against the Boost binaries.  It does not seem to refer to the boost binaries linking against something else.  
  
It took me a long time to figure out why ASAN failed with Fiber until I finally found this [issue](https://github.com/boostorg/context/issues/70).  Maybe my suggested change will also help other users.

---

## Comment 1

> Username: mathbunnyru  
> Created at: 2025-11-14 15:13:15 UTC  
> Url: https://github.com/boostorg/fiber/issues/289#issuecomment-3533230452  

We came across the same problem, and it wasn't obvious at all. Spent quite some time trying to figure out what's going on
