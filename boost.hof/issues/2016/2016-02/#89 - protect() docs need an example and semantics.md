# #89 - protect() docs need an example and semantics [Closed]

> Username: tzlaine  
> Created at: 2016-02-09 14:53:40 UTC  
> Updated at: 2017-01-31 21:18:34 UTC  
> Closed at: 2017-01-31 21:18:34 UTC  
> Url: https://github.com/boostorg/hof/issues/89  

Preferably, the sample code should show what would happen with or without protecting a particular function call.

---

## Comment 1

> Username: viboes  
> Created at: 2016-03-06 15:42:43 UTC  
> Url: https://github.com/boostorg/hof/issues/89#issuecomment-192917264  

The semantics is missing also.

---

## Comment 2

> Username: pfultz2  
> Created at: 2016-09-10 18:13:49 UTC  
> Url: https://github.com/boostorg/hof/issues/89#issuecomment-246126834  

I've added an example. It is still missing semantics. The lazy adaptor is missing semantics as well. I am not sure the best way to show the semantics. I'll probably need to use placeholders, so something like this:  
  
``` cpp  
lazy(f)(lazy(g)(_1))(x) == f(g(x))  
lazy(f)(protect(lazy(g)(_1)))() == f(lazy(g)(_1))  
```  
  
I am not sure it is the clearest, though.
