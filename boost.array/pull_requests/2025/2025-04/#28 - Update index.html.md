# #28 Update index.html [Merged]

> Username: ashtum  
> Created at: 2025-04-03 10:57:44 UTC  
> Updated at: 2025-04-03 11:17:01 UTC  
> Merged at: 2025-04-03 11:10:17 UTC  
> Closed at: 2025-04-03 11:10:17 UTC  
> Url: https://github.com/boostorg/array/pull/28  



---

## Comment 1

> Username: pdimov  
> Created_at: 2025-04-03 11:09:37 UTC  
> Url: https://github.com/boostorg/array/pull/28#issuecomment-2775407046  

Interestingly, the current index.html appears to work, even though it's no longer "correct". I wonder why.

---

## Comment 2

> Username: ashtum  
> Created_at: 2025-04-03 11:17:00 UTC  
> Url: https://github.com/boostorg/array/pull/28#issuecomment-2775424498  

> Interestingly, the current index.html appears to work, even though it's no longer "correct". I wonder why.  
  
I believe it is the meta refresh tag that performs the redirect, and its URL is correct. I fixed the link URL because that's what [boost-gecko](https://github.com/cppalliance/boost-gecko/) uses to reach the related documentation pages.  
  
```HTML  
<meta http-equiv="refresh" content="0; URL=doc/html/array.html">  
```

---
