# #1350 - Buffer control during HTTP reads [Closed]

> Username: vinniefalco  
> Created at: 2018-12-04 14:45:07 UTC  
> Updated at: 2022-06-16 16:24:06 UTC  
> Closed at: 2022-06-16 16:24:06 UTC  
> Url: https://github.com/boostorg/beast/issues/1350  

Documentation needs to explain how to control the size of HTTP reads. This could also be improved with a non-blocking implementation. We might want a _CompletionCondition_ interface to give users precise control, as well as having `reserve` methods on dynamic buffers.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-06-16 16:24:06 UTC  
> Url: https://github.com/boostorg/beast/issues/1350#issuecomment-1157873972  

We aren't doing this anymore
