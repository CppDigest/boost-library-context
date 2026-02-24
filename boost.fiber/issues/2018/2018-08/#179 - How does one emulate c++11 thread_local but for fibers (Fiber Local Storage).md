# #179 - How does one emulate c++11 thread_local but for fibers (Fiber Local Storage)? [Closed]

> Username: devshgraphicsprogramming  
> Created at: 2018-08-11 12:02:56 UTC  
> Updated at: 2018-08-14 12:21:48 UTC  
> Closed at: 2018-08-11 19:49:11 UTC  
> Url: https://github.com/boostorg/fiber/issues/179  

Since a fiber may wake up on a different thread, thread_local is useless.  
  
I have a few std:: compatible allocators which use thread_local in order to be lockless, however they would break if used with fibers.  
  
I know that windows has Fiber Local Storage, is there any way to do this in boost?

---

## Comment 1

> Username: olk  
> Created at: 2018-08-11 19:49:11 UTC  
> Url: https://github.com/boostorg/fiber/issues/179#issuecomment-412297732  

You could use fiber_specific_ptr: https://www.boost.org/doc/libs/1_68_0/libs/fiber/doc/html/fiber/fls.html

---

## Comment 2

> Username: devshgraphicsprogramming  
> Created at: 2018-08-14 10:51:21 UTC  
> Url: https://github.com/boostorg/fiber/issues/179#issuecomment-412832306  

Is there no faster way to do it than a lookup in an std::map !?  
  
I mean at least on windows it would perform far faster using the built-in system API.

---

## Comment 3

> Username: devshgraphicsprogramming  
> Created at: 2018-08-14 11:03:16 UTC  
> Updated at: 2018-08-14 11:04:14 UTC  
> Url: https://github.com/boostorg/fiber/issues/179#issuecomment-412835071  

On a note, if all global/static fiber-local variables would be declared via a wrapper class that supported some metaprogramming and manipulation of a global counter through the static object's initializers then the total space required for all `thread_local`-like could be known either at compile-time or at least just before `main()` would be called.   
  
So it would be possible to prepend "shadow stack space" to the allocated context's stack and retrieve all FLS variables by simple arithmetic in almost exactly the same way as TLS works.  
  
P.S. Or count FLS data storage size against the stack limit, whatever you choose.

---

## Comment 4

> Username: olk  
> Created at: 2018-08-14 11:53:25 UTC  
> Url: https://github.com/boostorg/fiber/issues/179#issuecomment-412846509  

You can only use the Win-Fiber API if boost.context uses it as its implementation detail (property win-fib). - otherwise it's not possible. On Linux/Unix you don't have an system API that provides fiber-local-storage.

---

## Comment 5

> Username: devshgraphicsprogramming  
> Created at: 2018-08-14 12:21:48 UTC  
> Url: https://github.com/boostorg/fiber/issues/179#issuecomment-412853101  

By now I'm aware of the Linux/Unix problem, but FLS should not be that hard to emulate with extra reserved space in the context's stack, simply because all TLS (and windows FLS) variables have to be static or global.
