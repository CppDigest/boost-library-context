# #42 - Why was fcontext moved into detail::/deprecated? Can it be public again? [Closed]

> Username: forrestv  
> Created at: 2017-01-26 15:25:52 UTC  
> Updated at: 2017-01-26 16:49:19 UTC  
> Closed at: 2017-01-26 16:31:30 UTC  
> Url: https://github.com/boostorg/context/issues/42  

Can `fcontext` please be made part of the public API (and stabilized)?  
  
The new `execution_context` removes too much freedom for me. Primarily, I don't want uncaught exceptions to be caught and rethrown in the parent coroutine, as this messes up stack traces from [backward-cpp](https://github.com/bombela/backward-cpp).  
  
`fcontext` alone provided a great, clean, portable replacement for the deprecated POSIX `ucontext` functions.

---

## Comment 1

> Username: olk  
> Created at: 2017-01-26 16:31:30 UTC  
> Url: https://github.com/boostorg/context/issues/42#issuecomment-275435942  

no, I'm sorry - boost.context is a C++-only library

---

## Comment 2

> Username: forrestv  
> Created at: 2017-01-26 16:41:46 UTC  
> Url: https://github.com/boostorg/context/issues/42#issuecomment-275439116  

Hm? I'm using it from C++.

---

## Comment 3

> Username: forrestv  
> Created at: 2017-01-26 16:49:19 UTC  
> Url: https://github.com/boostorg/context/issues/42#issuecomment-275441398  

My point isn't that I hate object-oriented interfaces; it's that `execution_context`, even though it's just a thin wrapper over `fcontext`, does remove some amount of control/flexibility that I need.  
  
One solution to that is exposing `fcontext` so that users can do exactly what they want with contexts, but another might be adding some of that flexibility to `execution_context`. Should I start a separate issue about the specific use cases that `execution_context` can't serve that `fcontext` can?
