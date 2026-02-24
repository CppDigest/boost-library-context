# #745 Merge to Master for 1.90 [Merged]

> Username: mborland  
> Created at: 2025-10-23 08:52:33 UTC  
> Updated at: 2025-10-26 11:17:27 UTC  
> Merged at: 2025-10-26 11:17:27 UTC  
> Closed at: 2025-10-26 11:17:27 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/745  



---

## Comment 1

> Username: ckormanyos  
> Created_at: 2025-10-25 09:29:50 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/745#issuecomment-3446390005  

Hi @mborland see #746, which should (or is expected to) handle the `-mbig-obj` problems on the two complex-adaptor tests.  
  
The failures in special function C99 edge cases are from [boostorg/math/pull/1315](https://github.com/boostorg/math/pull/1315), which repaired negative zero for `erf` and `erfc`. Since your actual CI for Multiprecision ran _Prior_ to the merge of Math to master, this probably hit the Multiprecision tests at that earlier state of Math.  
  
Cc: @jzmaddock

---

## Comment 2

> Username: ckormanyos  
> Created_at: 2025-10-25 18:47:30 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/745#issuecomment-3447170350  

Hi Matt (@mborland) this one runs green now. We needed Math to be updated, as mentioned.  
  
The other unrelated problem with MinGW 15.2.0 and the big objects attribute is solved. My Jamfile solution, however, may have been a bit heavy-handed.  
  
So if you could take a look at this one at a time of your convenience, we can either modify the MinGW fix or let it ride, both or either of which are fine by me.  
  
Cc: @jzmaddock

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2025-10-26 09:29:50 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/745#issuecomment-3448262533  

>The other unrelated problem with MinGW 15.2.0 and the big objects attribute is solved. My Jamfile solution, however, may have been a bit heavy-handed.  
  
It's a really annoying issue, that keeps resurfacing every time some random thing changes, we've just got to do what we've got to do here.  Go for it.

---

## Comment 4

> Username: ckormanyos  
> Created_at: 2025-10-26 11:10:30 UTC  
> Updated_at: 2025-10-26 11:12:23 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/745#issuecomment-3448426801  

>> My Jamfile solution, however, may have been a bit heavy-handed.  
  
> we've just got to do what we've got to do here. Go for it.  
  
Indeed. Thanks John. I was a bit uncertain. I got the impression CI had problems until the addition of [this line](https://github.com/boostorg/multiprecision/blob/29a192ec70fc94628c3ed382ec08861581c634f3/test/Jamfile.v2#L62). Did I just turn on big-object for all the MinGW runs? I suppose that wouldn't even really matter anyway. But I'm not sure if that line is/was really needed?  
  
My other changes seemed easier to understand.  
  
Cc: @mborland

---

## Comment 5

> Username: mborland  
> Created_at: 2025-10-26 11:17:16 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/745#issuecomment-3448432668  

`big-obj` is now enabled for all GCC on Windows which is fine. I'm going to merge this since we're back to green with the synchronization from Math's merge

---
