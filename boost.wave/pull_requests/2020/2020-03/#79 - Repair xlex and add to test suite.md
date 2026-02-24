# #79 Repair xlex and add to test suite [Merged]

> Username: jefftrull  
> Created at: 2020-03-12 07:01:02 UTC  
> Updated at: 2020-03-13 05:37:46 UTC  
> Merged at: 2020-03-13 05:37:46 UTC  
> Closed at: 2020-03-13 05:37:46 UTC  
> Url: https://github.com/boostorg/wave/pull/79  

Fix xlex and the token_statistics sample by:  
  
- Changing space handling to match lexertl so lexer test passes  
- Adding xlex to test Jamfile  
- Adding remedial line/column tracking to xlex (positions were often reported as 0:-1)  
- Fixing token_statistics id index for counting

---

## Review 1 [Approved]

> Username: hkaiser  
> Created_at: 2020-03-13 00:49:08 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/wave/pull/79#pullrequestreview-373985778  

LGTM, thanks!

---
