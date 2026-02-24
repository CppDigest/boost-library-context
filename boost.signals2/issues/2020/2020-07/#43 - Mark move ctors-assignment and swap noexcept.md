# #43 - Mark move ctors/assignment and swap noexcept [Open]

> Username: Lastique  
> Created at: 2020-07-19 19:53:39 UTC  
> Updated at: 2020-07-20 20:26:18 UTC  
> Url: https://github.com/boostorg/signals2/issues/43  

In connection and signal classes, move constructors/assignment and `swap` are not marked `noexcept`, which prevents the user's types that have objects of these types as members to have `noexcept` constructors/assignment. It also prevents optimizations in standard library containers.

---

## Comment 1

> Username: fmhess  
> Created at: 2020-07-20 18:49:59 UTC  
> Url: https://github.com/boostorg/signals2/issues/43#issuecomment-661269578  

I think this is fixed with commit 3c20f17390f882b09484d038fe2e836ab69164ab on the develop branch.  You don't happen to have test code for the noexcept behavior?

---

## Comment 2

> Username: Lastique  
> Created at: 2020-07-20 19:28:12 UTC  
> Url: https://github.com/boostorg/signals2/issues/43#issuecomment-661288185  

Thanks. No, I don't have a test. In my code base I just had a connection object as a member of a class, which prevented that class from having `noexcept` special functions, which was important.  
  
While we're at it, could you also mark default constructors `noexcept` where possible? This has no bearing on containers, but still does affect user's classes.

---

## Comment 3

> Username: fmhess  
> Created at: 2020-07-20 20:17:54 UTC  
> Url: https://github.com/boostorg/signals2/issues/43#issuecomment-661311083  

I've added marked more connection constructors noexcept in commit fb300ad723d8a28e9245e79c90b7bb5f0c976547  
Do you care about the signal default constructor?  It might be possible to conditionally mark it as noexcept depending on the noexcept status of the default constructors of the signal's various template types, but it is more involved than just slapping on a specifier.

---

## Comment 4

> Username: Lastique  
> Created at: 2020-07-20 20:26:18 UTC  
> Url: https://github.com/boostorg/signals2/issues/43#issuecomment-661315078  

Thanks again.  
  
In my current code base with rather modest use of Boost.Signals2 the signal's default constructor doesn't matter. I was under impression that it contains a few pointers internally, which could be initialized to null or something like that. If it does something more complex that can throw, then surely marking it would not be a good idea.
