# #112 - when_all and when_any should probably not spawn a thread [Open]

> Username: HadrienG2  
> Created at: 2017-01-12 15:02:55 UTC  
> Updated at: 2017-09-18 16:33:06 UTC  
> Url: https://github.com/boostorg/thread/issues/112  

The current implementation of boost::when_all and boost::when_any spawns a thread that calls wait_for_all or wait_for_any. This behaviour can result in explosive creation of OS threads when these functions are called often, which is probably not what the user expects since asynchronous primitives like futures are normally used as an alternatives to spawning lots of waiting threads.  
  
As a simple workaround, future continuations can be used instead to asynchronously set the output future of when_all or when_any as appropriate when its input futures become ready. More efficient solutions probably exist to someone familiar with the Boost future implementation.

---

## Comment 1

> Username: HadrienG2  
> Created at: 2017-01-14 08:55:24 UTC  
> Url: https://github.com/boostorg/thread/issues/112#issuecomment-272611484  

If you are interested, I have a mock continuation-based implementation of when_all and when_any around. I can figure out a way to release it on github.

---

## Comment 2

> Username: viboes  
> Created at: 2017-01-14 09:57:05 UTC  
> Url: https://github.com/boostorg/thread/issues/112#issuecomment-272614168  

I would be interested in any improvement to the current implementation.  
If you can propose a github Pull request this would be much appreciated.

---

## Comment 3

> Username: HadrienG2  
> Created at: 2017-01-22 12:09:37 UTC  
> Url: https://github.com/boostorg/thread/issues/112#issuecomment-274327144  

Looking closer at my implementation, I notice that our when_any signature mismatches. I use the C++ Concurrency TS signature, which gives you an index into the output vector of future to tell you which future became ready. Whereas the current Boost.Thread signature only gives you the vector, and leaves it up to you to look up which future became ready.  
  
I personally thinks that the Concurrency TS proposal is better, because it avoids looking up an information which the when_any implementation already knows. But changing the Boost interface in this direction might break some client code. What is the official policy to handle interface-breaking changes in Boost?

---

## Comment 4

> Username: viboes  
> Created at: 2017-09-18 16:32:30 UTC  
> Url: https://github.com/boostorg/thread/issues/112#issuecomment-330279075  

Boost.Thread was based on an earlier proposal than the Concurrency TS. I don't remember which one. I don't believe none of them is the good one, and I don't know yet which one would finish on the C++ standard.   
  
When breaking user code we need a new version and continue providing older versions :(  
For the time being I would prefer to don't change the interface.

---

## Comment 5

> Username: viboes  
> Created at: 2017-09-18 16:33:06 UTC  
> Url: https://github.com/boostorg/thread/issues/112#issuecomment-330279255  

I was wondering if this is not a consequence of issue #111.
