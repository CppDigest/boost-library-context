# #44 Win32 coalesced timers support [Merged]

> Username: ned14  
> Created at: 2015-02-04 14:23:21 UTC  
> Updated at: 2015-02-04 18:15:22 UTC  
> Merged at: 2015-02-04 18:14:40 UTC  
> Closed at: 2015-02-04 18:14:40 UTC  
> Url: https://github.com/boostorg/thread/pull/44  

Related to issue #9856.  
  
Doesn't fail any timer related unit tests. BTW Vicente your when_any testing is failing heavily with bad iterator dereferencing. I'll email you the failure log.

---

## Comment 1

> Username: viboes  
> Created_at: 2015-02-04 18:03:43 UTC  
> Url: https://github.com/boostorg/thread/pull/44#issuecomment-72905412  

I don't master the windows interface.   
How this patch improves the current situation? Does it ensure that there are less than 10% of spurious notifications?  
I let you do the merge as I can not test it.

---

## Comment 2

> Username: ned14  
> Created_at: 2015-02-04 18:14:15 UTC  
> Url: https://github.com/boostorg/thread/pull/44#issuecomment-72907361  

To be honest, this patch makes very little difference to issue #9856, about the same number of timed waits return early as before for short duration waits. I marked that issue as wontfix, because if Windows is returning early we can't do much but pass that on.  
  
What it gains though is much better power consumption on mobile devices as the system can choose to extend the timed wait by up to 5%. And for longer waits e.g. 1 second, I've noticed Windows is almost exactly accurate to your request now whereas before it was going on average +30% over the requested amount.

---
