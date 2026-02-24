# #641 Fix Clang 9 build on GitHub Actions [Merged]

> Username: striezel  
> Created at: 2022-04-06 21:01:38 UTC  
> Updated at: 2022-04-07 18:08:57 UTC  
> Merged at: 2022-04-07 18:01:03 UTC  
> Closed at: 2022-04-07 18:01:03 UTC  
> Url: https://github.com/boostorg/gil/pull/641  

### Description  
  
The name of the clang-9 package was missing, so it was not installed. This PR fixes it, so that Clang 9 gets installed an can be used during CI.  
  
### References  
  
None.  
  
### Tasklist  
  
- [x] Ensure Clang 9 CI builds passed  
- [ ] Review and approve

---

## Comment 1

> Username: sdebionne  
> Created_at: 2022-04-07 14:25:01 UTC  
> Url: https://github.com/boostorg/gil/pull/641#issuecomment-1091807437  

@striezel I just want to say thank you for looking into our CI issues!

---

## Comment 2

> Username: mloskot  
> Created_at: 2022-04-07 14:53:31 UTC  
> Url: https://github.com/boostorg/gil/pull/641#issuecomment-1091840900  

Yes, we needed it, and we still need.  
  
@striezel @sdebionne @lpranam & others, By the way, please have a look at https://github.com/boostorg/gil/issues/642 All input welcome.

---

## Comment 3

> Username: striezel  
> Created_at: 2022-04-07 16:11:11 UTC  
> Url: https://github.com/boostorg/gil/pull/641#issuecomment-1091931375  

By the way, the Clang 9 build passed, so this should be ready for review.  
  
> @striezel I just want to say thank you for looking into our CI issues!  
  
You're welcome.

---
