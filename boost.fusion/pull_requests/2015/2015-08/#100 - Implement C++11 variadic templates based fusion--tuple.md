# #100 Implement C++11 variadic templates based fusion::tuple. [Merged]

> Username: Flast  
> Created at: 2015-08-26 14:15:07 UTC  
> Updated at: 2015-08-28 13:39:28 UTC  
> Merged at: 2015-08-27 06:55:11 UTC  
> Closed at: 2015-08-27 06:55:11 UTC  
> Url: https://github.com/boostorg/fusion/pull/100  

This is last container update for support variadics.

---

## Comment 1

> Username: djowel  
> Created_at: 2015-08-27 00:06:09 UTC  
> Url: https://github.com/boostorg/fusion/pull/100#discussion_r38050323  

Shouldn't this simply be a alias template?

---

## Comment 2

> Username: K-ballo  
> Created_at: 2015-08-27 00:21:02 UTC  
> Url: https://github.com/boostorg/fusion/pull/100#discussion_r38051224  

Alias templates are not always supported when variadics are, but even if they were these types are documented as different things, and making them the same could/would be problematic for partial specializations.  
  
Furthermore, there's the question of how much should `fusion::tuple` try to be like `std::tuple`, specially considering `fusion::tuple` started as a TR1 implementation of `tuple`. Otherwise, there might not be much sense in keeping this around.

---

## Comment 3

> Username: djowel  
> Created_at: 2015-08-27 00:45:56 UTC  
> Url: https://github.com/boostorg/fusion/pull/100#discussion_r38052609  

Good points. I think the last point makes sense "there might not be much sense in keeping this around". But let's keep it for now.

---
