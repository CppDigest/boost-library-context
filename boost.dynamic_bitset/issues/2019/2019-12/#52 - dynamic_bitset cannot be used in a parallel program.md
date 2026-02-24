# #52 - dynamic_bitset cannot be used in a parallel program [Closed]

> Username: alexanderkjeldaas  
> Created at: 2019-12-17 14:25:23 UTC  
> Updated at: 2019-12-19 11:58:56 UTC  
> Closed at: 2019-12-19 11:58:56 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/issues/52  

Ok, it can be used, but not in any reasonably efficient manner.  
  
A natural way to use a bitset in a parallel program is to work on sections of the bitset.  
  
This requires being able to see a "view" of the bitset.  
  
An option to create a read-only view into a bitset in O(1) time would suffice in a lot of cases.  
  
Consider the case where a set of bitsets are processed in parallel and a various sections of the bitsets are processed.  In such a scenario, the threads can have local bitsets as storage, but would need to read slices of shared bitsets.  
  
Writing back to a bitset in parallel could be more problematic, but a slice API that snaps to the closest block size could be used.

---

## Comment 1

> Username: mclow  
> Created at: 2019-12-17 15:42:29 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/issues/52#issuecomment-566597171  

I'm having a hard time seeing this as an "issue".  A major piece of new functionality being requested, sure - but an 'issue'?

---

## Comment 2

> Username: alexanderkjeldaas  
> Created at: 2019-12-17 19:14:56 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/issues/52#issuecomment-566707241  

I don't understand. Is there a tag needed for a feature request?

---

## Comment 3

> Username: glenfe  
> Created at: 2019-12-19 00:04:23 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/issues/52#issuecomment-567268311  

@alexanderkjeldaas, what you're asking for isn't so much a feature for `boost::dynamic_bitset` as much as it is its own (new) class. The Boost mailing list (http://lists.boost.org/) is probably the best place to start with that.
