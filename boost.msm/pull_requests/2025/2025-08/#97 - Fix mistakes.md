# #97 Fix mistakes [Merged]

> Username: ivanpanch  
> Created at: 2025-08-21 23:09:56 UTC  
> Updated at: 2026-01-29 00:23:25 UTC  
> Merged at: 2025-10-15 15:59:58 UTC  
> Closed at: 2025-10-15 15:59:58 UTC  
> Url: https://github.com/boostorg/msm/pull/97  

I have corrected some minor mistakes.

---

## Review 1 [Commented]

> Username: chandryan  
> Created_at: 2025-09-18 05:48:25 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/msm/pull/97#pullrequestreview-3237342743  

📁 doc/src/msm.xml

```diff
 242 |                         concepts to model, but not enough to make them first-class citizens. The
 243 |-                         terminate pseudo state terminates the execution of a state machine (MSM
 243 |+                         terminate pseudo state terminates the execution of a state machine. (MSM
```

> Username: chandryan  
> Created_at: 2025-09-18 05:31:21 UTC  
> Updated_at: 2025-09-18 05:48:25 UTC  
> Url: https://github.com/boostorg/msm/pull/97#discussion_r2357560778  

I think this was correct as it was (puncutation comes after the parentheses).

> Username: ivanpanch  
> Created_at: 2025-09-18 20:08:43 UTC  
> Updated_at: 2025-09-18 20:17:26 UTC  
> Url: https://github.com/boostorg/msm/pull/97#discussion_r2361021909  

My problem was that there were two sentences ending with a full stop inside the parentheses even though the parentheses were integrated into a single sentence. This would also be okay: `… terminates the execution of a state machine (MSM handles this slightly differently; the state machine is not destroyed but no further event processing occurs).`

> Username: chandryan  
> Created_at: 2025-10-09 22:34:26 UTC  
> Url: https://github.com/boostorg/msm/pull/97#discussion_r2418097395  

That would be a good alternative 👍

> Username: ivanpanch  
> Created_at: 2025-10-13 12:24:07 UTC  
> Url: https://github.com/boostorg/msm/pull/97#discussion_r2426194147  

Done!

> Username: chandryan  
> Created_at: 2025-10-14 19:11:38 UTC  
> Url: https://github.com/boostorg/msm/pull/97#discussion_r2430172019  

Nice, thanks!  
  
@henry-ch can we merge this one?

> Username: henry-ch  
> Created_at: 2025-10-15 07:37:05 UTC  
> Url: https://github.com/boostorg/msm/pull/97#discussion_r2431451863  

yes, thanks.

---

📁 doc/src/msm.xml

```diff
1479 |                     <para>But how to detect the digit event? We would like to avoid defining 10
1480 |-                         transitions on char_0, char_1... between two states as it would force us to
1480 |+                         transitions on char_0, char_1 ... between two states as it would force us to
```

> Username: chandryan  
> Created_at: 2025-09-18 05:38:00 UTC  
> Updated_at: 2025-09-18 05:48:25 UTC  
> Url: https://github.com/boostorg/msm/pull/97#discussion_r2357569582  

I think this one was correct the way it was.

> Username: ivanpanch  
> Created_at: 2025-09-18 20:10:55 UTC  
> Updated_at: 2025-09-18 20:10:56 UTC  
> Url: https://github.com/boostorg/msm/pull/97#discussion_r2361030888  

Okay, that is really a matter of style; I reverted it.


---
