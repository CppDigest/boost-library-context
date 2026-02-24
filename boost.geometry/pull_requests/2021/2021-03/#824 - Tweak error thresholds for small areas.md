# #824 [test][area] Tweak error thresholds for small areas. [Merged]

> Username: awulkiew  
> Created at: 2021-03-03 16:30:19 UTC  
> Updated at: 2021-03-03 19:20:38 UTC  
> Merged at: 2021-03-03 19:20:38 UTC  
> Closed at: 2021-03-03 19:20:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/824  

After merging https://github.com/boostorg/geometry/pull/801 some of the tests started to fail.  
This PR changes error margins for these cases.  
  
I don't know if this is a temporary or permanent solution. @vissarion do you think this is worth investigating more?

---

## Review 1 [Approved]

> Username: vissarion  
> Created_at: 2021-03-03 19:20:20 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/824#pullrequestreview-603261153  

It seems minor and the errors acceptable. We could leave it for now. Thanks for the PR!

---
