# #2 Drop the const_iterator [Merged]

> Username: LEW21  
> Created at: 2013-12-18 21:17:44 UTC  
> Updated at: 2014-08-16 21:27:17 UTC  
> Merged at: 2013-12-19 08:30:38 UTC  
> Closed at: 2013-12-19 08:30:38 UTC  
> Url: https://github.com/boostorg/coroutine/pull/2  

const_iterator, by definition, should not modify the iterated object, and it's impossible not to modify the coroutine while iterating. (And that's why it breaks the const-correctness, so const_cast in the constructor is needed.)  
  
I can't find a valid use case for that const_iterator, so I think it's simply unnecessary and can be safely removed.

---
