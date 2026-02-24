# #736 Karma: real generator off by a magnitude due to log10 rounding up [Merged]

> Username: Kojoley  
> Created at: 2022-09-16 03:16:21 UTC  
> Updated at: 2023-10-14 22:24:08 UTC  
> Merged at: 2022-09-22 21:05:16 UTC  
> Closed at: 2022-09-22 21:05:16 UTC  
> Url: https://github.com/boostorg/spirit/pull/736  

Closes #735  
Closes #529

---

## Comment 1

> Username: Kojoley  
> Created_at: 2022-09-22 15:24:06 UTC  
> Url: https://github.com/boostorg/spirit/pull/736#issuecomment-1255186987  

I'll merge this on weekend if there is no objection. Better to try out the fix than to do nothing.

---

## Review 2 [Approved]

> Username: hkaiser  
> Created_at: 2022-09-22 19:01:51 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/spirit/pull/736#pullrequestreview-1117544987  

LGTM, thanks!

---

## Review 3 [Commented]

> Username: friedrichatgc  
> Created_at: 2023-10-14 22:24:08 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/spirit/pull/736#pullrequestreview-1678504320  

This PR leads now to wrong outputs of other values:  
for example 3.1 is output as the string "**3.01**000000000000001"  
  
This still only happens if the precision is >=16.  
(but a precision of 16 is needed for example to get bit-identical values for float-string-float round-trips; so restricting to a precision of 15, as also discussed, is no option for me)

---
