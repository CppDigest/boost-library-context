# #2 Fix errno usage [Closed]

> Username: Lastique  
> Created at: 2014-03-14 06:38:09 UTC  
> Updated at: 2014-07-14 12:16:15 UTC  
> Closed at: 2014-04-08 17:45:45 UTC  
> Url: https://github.com/boostorg/random/pull/2  

errno should be captured immediately at error() beginning so that it is not modified by later operations, such as string concatenations. Fixes #8245.

---

## Comment 1

> Username: Lastique  
> Created_at: 2014-04-08 17:45:45 UTC  
> Url: https://github.com/boostorg/random/pull/2#issuecomment-39879172  

The fix has been applied by a separate commit.

---
