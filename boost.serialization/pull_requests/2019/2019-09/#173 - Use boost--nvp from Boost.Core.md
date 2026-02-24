# #173 Use boost::nvp from Boost.Core [Merged]

> Username: glenfe  
> Created at: 2019-09-04 15:26:46 UTC  
> Updated at: 2019-09-28 04:37:23 UTC  
> Merged at: 2019-09-28 04:37:22 UTC  
> Closed at: 2019-09-28 04:37:22 UTC  
> Url: https://github.com/boostorg/serialization/pull/173  

All your tests pass on my Travis queue: https://travis-ci.org/glenfe/boost.serialization/builds/580758159

---

## Comment 1

> Username: glenfe  
> Created_at: 2019-09-04 15:27:08 UTC  
> Url: https://github.com/boostorg/serialization/pull/173#issuecomment-527952882  

The Appveyor failures with GCC static are not related to this change.

---

## Comment 2

> Username: robertramey  
> Created_at: 2019-09-04 17:31:10 UTC  
> Url: https://github.com/boostorg/serialization/pull/173#issuecomment-528003885  

I'm OK with this - but I'd like to spend a little time thinking about it before I merge it.  As I've said - I think that shuffling around components to address the dependency "problem" is not an effective strategy or even a good idea.  Never the less, I've been outvoted on this so I'm inclined to go along at least until I can create a comprehensive solution to the problem - which I know the outlines of but haven't had time to work on.  It's low priority since it won't be popular either.

---

## Comment 3

> Username: glenfe  
> Created_at: 2019-09-04 17:53:48 UTC  
> Url: https://github.com/boostorg/serialization/pull/173#issuecomment-528012561  

I know, and agree. In the interim  - until your solution is completed and realized - with the way things are: This would enable a more than a few things to move forward and make progress (all without affecting existing users).

---
