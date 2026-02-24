# #270 - Change boost::when_any's return type [Closed]

> Username: tdauth  
> Created at: 2019-02-06 10:23:48 UTC  
> Updated at: 2019-02-06 19:53:00 UTC  
> Closed at: 2019-02-06 19:53:00 UTC  
> Url: https://github.com/boostorg/thread/issues/270  

What's the point of returning the whole collection of futures? How can I detect which future has been completed first and why do I need the whole collection?  
Look at Folly. They provide the function collectAny() which returns only the result of the first completed future and its index so one can trace back which future has been completed.  
  
You could also add combinators like collectAnyWithoutException, collectN, collectNWithoutException and fallbackTo (see Scala library).

---

## Comment 1

> Username: viboes  
> Created at: 2019-02-06 19:53:00 UTC  
> Url: https://github.com/boostorg/thread/issues/270#issuecomment-461163446  

The interface of Boost.Thread was based on an old proposal for the standard that is now part of the Concurrency V2 TS.  
  
If you are happy with Folly please use it.  
  
If you believe that we need something better in Boost please, propose it.  
  
If you believe that Boost.thread can provide something better, please propose a patch.  
  
Hopping I'm clear on what I can and you can do. We are not playing here. If you want I can give you some interesting references of what you can do in addition.  
  
So, in summary, this is not the way we work here, in Boost.
