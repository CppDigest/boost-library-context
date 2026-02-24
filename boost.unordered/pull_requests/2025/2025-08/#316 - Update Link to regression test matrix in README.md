# #316 Update Link to regression test matrix in README [Merged]

> Username: Flamefire  
> Created at: 2025-08-25 15:42:30 UTC  
> Updated at: 2025-09-19 07:15:25 UTC  
> Merged at: 2025-09-19 07:07:04 UTC  
> Closed at: 2025-09-19 07:07:04 UTC  
> Url: https://github.com/boostorg/unordered/pull/316  



---

## Comment 1

> Username: joaquintides  
> Created_at: 2025-09-18 16:56:53 UTC  
> Url: https://github.com/boostorg/unordered/pull/316#issuecomment-3308557502  

Hi, once https://github.com/boostorg/website-v2/issues/1901 is merged, this fix is not strictly required (links now work), but the links you provide are cleaner. Would you mind replacing `regression.boost.io` with `regression.boost.org` before I merge? Thank you!

---

## Comment 2

> Username: Flamefire  
> Created_at: 2025-09-18 17:02:16 UTC  
> Url: https://github.com/boostorg/unordered/pull/316#issuecomment-3308580444  

> Would you mind replacing `regression.boost.io` with `regression.boost.org`  
  
Can do but what is the reason for that? From the linked issue:  
> configmap-nginx.yaml: location = /development/tests/master/developer/issues.html { return 301 https://regression.boost.io/master/developer/issues.html; }  
  
So it does use boost.io there too which is why I used it as the "canonical" URL

---

## Comment 3

> Username: joaquintides  
> Created_at: 2025-09-18 17:48:37 UTC  
> Url: https://github.com/boostorg/unordered/pull/316#issuecomment-3308788308  

If I click on   
  
https://regression.boost.org/master/developer/issues.html  
  
the browser displays "boost.org" rather than "boost.io":  
  
<img width="671" height="331" alt="imagen" src="https://github.com/user-attachments/assets/18526dff-1230-4ed6-b0ba-1e2c4606a638" />  
  
So, the boost.io domain is effectively unknown to the user. I understand boost.io will be removed in the future as everything should go under boost.org as it always did.

---

## Comment 4

> Username: Flamefire  
> Created_at: 2025-09-18 17:53:16 UTC  
> Updated_at: 2025-09-18 17:53:37 UTC  
> Url: https://github.com/boostorg/unordered/pull/316#issuecomment-3308809696  

I see, didn't know that. Thanks for the explanation

---
