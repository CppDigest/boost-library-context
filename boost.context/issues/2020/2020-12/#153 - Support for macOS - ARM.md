# #153 - Support for macOS / ARM [Closed]

> Username: dixlorenz  
> Created at: 2020-12-08 15:24:32 UTC  
> Updated at: 2020-12-10 14:06:07 UTC  
> Closed at: 2020-12-10 14:06:06 UTC  
> Url: https://github.com/boostorg/context/issues/153  

What are your plans for supporting Apple Silicon?

---

## Comment 1

> Username: olk  
> Created at: 2020-12-08 15:49:42 UTC  
> Url: https://github.com/boostorg/context/issues/153#issuecomment-740703173  

I've no access to Apple systems but you are welcome to provide a patch for macOS/ARM.

---

## Comment 2

> Username: dixlorenz  
> Created at: 2020-12-08 16:01:12 UTC  
> Url: https://github.com/boostorg/context/issues/153#issuecomment-740710845  

Unfortunately, I wouldn't even know where to start. From my little understanding "it" (whatever that even is) should be the same as iOS. I'd be happy to help or test something.

---

## Comment 3

> Username: dixlorenz  
> Created at: 2020-12-10 12:15:39 UTC  
> Url: https://github.com/boostorg/context/issues/153#issuecomment-742485023  

On my ARM MacBookPro I tried building boost as usual, just squeezing arch=arm64 everywhere; I didn't mess around with BOOST_USE_UCONTEXT or context-impl=ucontext at all. Boost built without problems I did see, my programs build, they are native and they run (using boost/fiber, not boost/context directly). A breakpoint set in fiber_entry (in context/fiber_fcontext.hpp) was hit, as far as I can tell it does use fcontext and not fall back to ucontext (I don't know if that could even happen). After a few hours of testing and running I want to say it works; there is one rare crash that I can't explain yet, working on it.  
  
There might be a bug somewhere, but *AFAICT* it basically works. The build system for boost definitely needs updating, but boost/context and boost/fiber seem to be just missing updated documentation.

---

## Comment 4

> Username: olk  
> Created at: 2020-12-10 12:43:06 UTC  
> Url: https://github.com/boostorg/context/issues/153#issuecomment-742497803  

ty for the information

---

## Comment 5

> Username: olk  
> Created at: 2020-12-10 12:44:56 UTC  
> Url: https://github.com/boostorg/context/issues/153#issuecomment-742498664  

Do you use 32bit or 64bit  ARM?

---

## Comment 6

> Username: dixlorenz  
> Created at: 2020-12-10 13:33:50 UTC  
> Url: https://github.com/boostorg/context/issues/153#issuecomment-742523884  

64 bit; I don't think macOS ARM 32 even exists

---

## Comment 7

> Username: olk  
> Created at: 2020-12-10 14:06:06 UTC  
> Url: https://github.com/boostorg/context/issues/153#issuecomment-742541776  

documentation has been updated, ty
