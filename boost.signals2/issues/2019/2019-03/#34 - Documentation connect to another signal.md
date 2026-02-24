# #34 - Documentation connect to another signal [Open]

> Username: tt4g  
> Created at: 2019-03-27 01:28:21 UTC  
> Updated at: 2019-03-28 00:35:55 UTC  
> Url: https://github.com/boostorg/signals2/issues/34  

Boost.Signals2 can connect to `boost::signals2::signal` object, I found test:  
https://github.com/boostorg/signals2/blob/e814fe03d98f30a6468bc28bcbec52302f5f32cf/test/signal_test.cpp#L143-L163  
  
If the connected signal object is deleted, auto disconnected.  
  
This feature is very useful but does not found documented.  
  
Adding documentation for this feature will help users.

---

## Comment 1

> Username: fmhess  
> Created at: 2019-03-28 00:15:29 UTC  
> Url: https://github.com/boostorg/signals2/issues/34#issuecomment-477394550  

I'll be pedantic for a moment and say it is mentioned in the description of the slot constructor:  
  
https://www.boost.org/doc/libs/1_66_0/doc/html/boost/signals2/slot.html#id-1_3_37_6_12_5_1_1_13-bb  
  
However, you are right the feature should be mentioned more prominently, probably in the tutorial.

---

## Comment 2

> Username: tt4g  
> Created at: 2019-03-28 00:35:55 UTC  
> Url: https://github.com/boostorg/signals2/issues/34#issuecomment-477398167  

I overlooked the description.  
  
Yes, I think it's very easy to understand if there is a tutorial.
