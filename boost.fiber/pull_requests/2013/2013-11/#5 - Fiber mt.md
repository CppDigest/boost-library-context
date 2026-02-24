# #5 Fiber mt [Merged]

> Username: nat-goodspeed  
> Created at: 2013-11-07 03:15:36 UTC  
> Updated at: 2016-06-06 11:36:33 UTC  
> Merged at: 2013-11-07 18:26:17 UTC  
> Closed at: 2013-11-07 18:26:17 UTC  
> Url: https://github.com/boostorg/fiber/pull/5  

Documentation pass with new queue API.  
  
Oliver, I indulged in a bit of wishful thinking / API design while documenting the new queue methods. Notably, I added queue_op_status returns to several methods. Of course that's only a suggestion; I do see that some of the push methods currently throw an exception if the queue is closed. It occurred to me, though, that a control fiber might launch a producer and consumer on distinct fibers, then some time later forcibly close() the queue they share.  
  
I also experimented with internal cross-references. In my ignorance of BoostBook machinery, I ended up removing formatting from certain heading directives (e.g. [heading Member function `joinable()`] becomes [heading Member function joinable]) because the formatting and parentheses extend the automatically-generated anchor name in ways that are ugly and hard to predict. Probably there's a mechanism for specifying a heading's anchor name explicitly, in which case we can replace the formatting.  
  
I haven't yet finished making a pass through internal cross-references.

---
