# #507 Fix tutorial documentation Jamroot/Jamfile confusion [Open]

> Username: ssam18  
> Created at: 2025-11-17 20:21:14 UTC  
> Updated at: 2025-11-30 14:56:04 UTC  
> Url: https://github.com/boostorg/python/pull/507  

I noticed the tutorial documentation had some confusing references about where the Jamroot file is located. The docs said the tutorial directory contains a Jamroot, but it actually has a Jamfile. The actual Jamroot is in the parent example directory.  
  
This was causing confusion because the link correctly pointed to `../example/Jamroot`, but the text suggested it was in the tutorial directory itself.  
  
**Changes:**  
- Corrected the file listing for the tutorial directory to show Jamfile instead of Jamroot  
- Clarified that the Jamroot is in the parent example directory  
- Updated the link description to make it clear where the file is located  
  
This should help people following the tutorial find the right files without confusion.  
  
Fixes boostorg/boost#1045

---

## Comment 1

> Username: ssam18  
> Created_at: 2025-11-17 20:21:39 UTC  
> Url: https://github.com/boostorg/python/pull/507#issuecomment-3543689816  

I was following the tutorial to learn Boost.Python and kept looking for a Jamroot file in the tutorial directory that didn't exist. Took me a while to realize it was actually a Jamfile, and the Jamroot was in the parent directory. Hope this clarification helps others avoid the same confusion

---

## Comment 2

> Username: ssam18  
> Created_at: 2025-11-30 14:56:04 UTC  
> Url: https://github.com/boostorg/python/pull/507#issuecomment-3592635457  

@danieljames , @daniel-w , @teeks99 , @steveire  - Can you please get a chance to review this PR?

---
