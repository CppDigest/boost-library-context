# #37 - define what a function is before defining what is a function adaptor [Closed]

> Username: viboes  
> Created at: 2015-10-04 01:41:02 UTC  
> Updated at: 2015-10-30 23:03:28 UTC  
> Closed at: 2015-10-30 23:03:28 UTC  
> Url: https://github.com/boostorg/hof/issues/37  

I believe that you need to define before what a function is before defining what is a function adaptor.  
  
"A function adaptor takes a function(or functions) and returns a new function "  
  
Is it a Callable?

---

## Comment 1

> Username: pfultz2  
> Created at: 2015-10-05 04:36:44 UTC  
> Url: https://github.com/boostorg/hof/issues/37#issuecomment-145428276  

That is left to each adaptor to define. Most adaptors use `FunctionObject`, however, there is a few that are different. That is why I just use function there.

---

## Comment 2

> Username: viboes  
> Created at: 2015-10-05 22:43:05 UTC  
> Url: https://github.com/boostorg/hof/issues/37#issuecomment-145689959  

So there is no concept of adaptor :(

---

## Comment 3

> Username: pfultz2  
> Created at: 2015-10-30 23:03:28 UTC  
> Url: https://github.com/boostorg/hof/issues/37#issuecomment-152670319  

There is almost a concept definition of an adaptor in the overview, at least to show how they work. However, there is no concept definition of an adaptor because each adaptor fulfills the concept of a `FunctionObject`. Just like iterator adaptors fulfill the concept of an iterator.
