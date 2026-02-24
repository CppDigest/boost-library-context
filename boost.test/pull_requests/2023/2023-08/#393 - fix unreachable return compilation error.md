# #393 fix unreachable return compilation error [Merged]

> Username: timmaraju  
> Created at: 2023-08-14 13:53:26 UTC  
> Updated at: 2024-05-31 08:45:47 UTC  
> Merged at: 2024-05-31 08:43:43 UTC  
> Closed at: 2024-05-31 08:43:43 UTC  
> Url: https://github.com/boostorg/test/pull/393  

On Visual Studio 16.9, the return was marked as unreachable because of the throw before

---

## Review 1 [Approved]

> Username: mborland  
> Created_at: 2023-08-14 13:57:41 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/test/pull/393#pullrequestreview-1576903106  

LGTM. I approved the CI run so we'll see what that has to say.

---

## Comment 2

> Username: timmaraju  
> Created_at: 2024-05-31 08:36:59 UTC  
> Url: https://github.com/boostorg/test/pull/393#issuecomment-2141504454  

@mborland Sorry for coming back so late to this, can we start the CI run again on this please?

---

## Comment 3

> Username: mborland  
> Created_at: 2024-05-31 08:45:46 UTC  
> Url: https://github.com/boostorg/test/pull/393#issuecomment-2141519259  

> @mborland Sorry for coming back so late to this, can we start the CI run again on this please?  
  
The only issue was with 4.6 which has been dropped since this PR opened.

---
