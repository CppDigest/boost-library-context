# #589 [TURNS] Disable start-turns, and take the closer point  [Merged]

> Username: barendgehrels  
> Created at: 2019-05-15 10:59:11 UTC  
> Updated at: 2019-07-18 11:57:24 UTC  
> Merged at: 2019-05-18 16:16:17 UTC  
> Closed at: 2019-05-18 16:16:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/589  

Closer point to measure distance, which makes that measurement more precise.

---

## Comment 1

> Username: awulkiew  
> Created_at: 2019-05-15 23:13:46 UTC  
> Url: https://github.com/boostorg/geometry/pull/589#issuecomment-492857809  

Is this PR needed in your future work on removing rescaling or is the intention fixing the issue https://github.com/boostorg/geometry/issues/588? This PR does not fix it, the reason is something else (see in the issue). If this change is not needed and it interferes with your work I suggest to discard it.

---

## Comment 2

> Username: barendgehrels  
> Created_at: 2019-05-16 16:42:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/589#issuecomment-493143674  

@awulkiew  thanks for your investigations.  
Though this PR does not fix the issue you reported, I'm still glad you mentioned it because it **does** solve issues in validity, when rescaling is turned off.  
Because of that, and because of all issues are now fixed in another way, I currently don't need start-turns and would prefer if they are not necessary. Code wise (less code) and because of what you said, if I disable them in validity, the turns become context-dependent.  
  
So I'm in favour of keeping this PR - but if you want to postpone it, so instead of disabling the code, removing it at all later (or keeping it in the end), that is also fine for me.

---

## Comment 3

> Username: awulkiew  
> Created_at: 2019-05-16 18:28:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/589#issuecomment-493181228  

>  but if you want to postpone it  
  
If you prefer this solution than the other I'm ok with that. Less code is better than more code, etc.

---

## Comment 4

> Username: vissarion  
> Created_at: 2019-05-17 13:08:31 UTC  
> Url: https://github.com/boostorg/geometry/pull/589#issuecomment-493447092  

Thanks. I am also ok if you want to merge this PR.    
  
However, I do not see the less code argument. Is this a result of this PR or enables you to remove unnecessary code?

---

## Comment 5

> Username: barendgehrels  
> Created_at: 2019-05-18 16:16:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/589#issuecomment-493688664  

About less code: it is indirectly a result of this PR because here we just disable start turns (without throwing away the code) and later I can either throw it away (preferred) or, if unfortunately necessary, re-enable it again but then I've also to distinguish between use cases (overlay-validation) without creating inconsistencies.  
  
Thanks, I will merge it.

---
