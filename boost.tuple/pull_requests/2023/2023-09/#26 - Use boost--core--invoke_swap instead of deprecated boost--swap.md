# #26 Use boost::core::invoke_swap instead of deprecated boost::swap [Closed]

> Username: Flamefire  
> Created at: 2023-09-11 07:31:40 UTC  
> Updated at: 2023-09-28 21:10:14 UTC  
> Closed at: 2023-09-28 19:03:00 UTC  
> Url: https://github.com/boostorg/tuple/pull/26  



---

## Comment 1

> Username: djowel  
> Created_at: 2023-09-14 06:33:01 UTC  
> Url: https://github.com/boostorg/tuple/pull/26#issuecomment-1718842331  

tell me if this is ready for merging

---

## Comment 2

> Username: Flamefire  
> Created_at: 2023-09-14 06:46:15 UTC  
> Url: https://github.com/boostorg/tuple/pull/26#issuecomment-1718856429  

Yeah, sorry for the commit spam, the fix is easy but looks like CI wasn't run for a while or the broken runs ignored, so I'm trying to fix that too. Might be easier to move to the files from Boost.CI at some point to get the updates from there when required.     
The failures are the same I ran into in https://github.com/boostorg/optional/pull/112 so I expect it to work now.

---

## Comment 3

> Username: pdimov  
> Created_at: 2023-09-28 19:03:00 UTC  
> Url: https://github.com/boostorg/tuple/pull/26#issuecomment-1739858371  

Merged #25 instead.

---
