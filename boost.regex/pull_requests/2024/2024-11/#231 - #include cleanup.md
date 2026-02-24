# #231 #include cleanup [Merged]

> Username: cmazakas  
> Created at: 2024-11-14 21:36:19 UTC  
> Updated at: 2024-11-25 15:51:52 UTC  
> Merged at: 2024-11-23 11:10:27 UTC  
> Closed at: 2024-11-23 11:10:27 UTC  
> Url: https://github.com/boostorg/regex/pull/231  

Update library header files to be fully self-contained so tools like clangd can work with the b2-generated compile commands database.  
  
Also update CI to run for `feature/**` branches.  
  
To handle circular #includes to make tooling happy, we defer to `#pragma once` when appropriate. The code here is a little over-engineered and can theoretically just be `#pragma once` but I wasn't sure how Regex was used in the wild.

---

## Comment 1

> Username: cmazakas  
> Created_at: 2024-11-22 22:40:49 UTC  
> Url: https://github.com/boostorg/regex/pull/231#issuecomment-2494991163  

@jzmaddock any thoughts on this?

---
