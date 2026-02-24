# #5 Documentation review for Boost.Coroutine feature/symmetric branch. [Closed]

> Username: nat-goodspeed  
> Created at: 2014-02-11 02:39:41 UTC  
> Updated at: 2014-02-11 18:02:12 UTC  
> Closed at: 2014-02-11 18:02:12 UTC  
> Url: https://github.com/boostorg/coroutine/pull/5  

Many small grammar tweaks, probably applicable to master branch too.  
  
The one substantive comment specifically about feature/symmetric: can we get  
away without yield_type::operator()?  
  
I think there may be a bug in the symmetric_coroutine merge example. I've changed the final copy-rest-of-elements loop. I've also propagated the comments in one instance to both instances.  
  
Can the specified stack size actually be equal to either minimum_stacksize() or maximum_stacksize(), or must it be strictly between them? Also, in a couple of preconditions, I think the sense of the comparison was misstated.  
  
I've added remarks and questions flagged with "Oliver:" in the documentation.

---
