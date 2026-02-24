# #640 Replace Ubuntu 16.04 with Ubuntu 18.04 in some GitHub Actions jobs [Merged]

> Username: striezel  
> Created at: 2022-04-06 17:34:21 UTC  
> Updated at: 2022-04-07 14:18:15 UTC  
> Merged at: 2022-04-07 11:49:03 UTC  
> Closed at: 2022-04-07 11:49:03 UTC  
> Url: https://github.com/boostorg/gil/pull/640  

### Description  
  
Ubuntu 16.04 is no longer available for GitHub Actions, because support for it has ended. However, some build jobs still try to use Ubuntu 16.04. This pull request tries to change that for those cases where a replacement with Ubuntu 18.04 is possible.  
  
Note: Some older Clang versions (Clang 3.8 and older) seem to be unavailable in the APT package repository, so those are not  
changed to 18.04. _This is intended._ Maybe tests for Clang 3.4 to Clang 3.8 should be removed, but that is probably worth another discussion.  
  
### References  
  
Ubuntu 16.04 has been removed from GitHub in 2021. See <https://github.com/actions/virtual-environments/issues/3287> for more information.  
  
### Tasklist  
  
- [x] Ensure updated CI builds pass  
- [x] Review and approve

---

## Comment 1

> Username: striezel  
> Created_at: 2022-04-06 17:43:40 UTC  
> Url: https://github.com/boostorg/gil/pull/640#issuecomment-1090550139  

I've just added another commit to update the Ubuntu version used in the Azure pipelines, too.  
Feel free to squash this into the first commit, if the change is acceptable.

---

## Review 2 [Approved]

> Username: mloskot  
> Created_at: 2022-04-06 19:08:41 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/640#pullrequestreview-933989719  

LGTM

---

## Comment 3

> Username: striezel  
> Created_at: 2022-04-06 20:43:21 UTC  
> Url: https://github.com/boostorg/gil/pull/640#issuecomment-1090774490  

Thanks for triggering the pipelines. :)  
  
Looks like all updated builds have passed successfully. Both Ubuntu pipelines on Azure have passed, and the four updated Ubuntu jobs on GitHub Actions have passed, too.

---

## Comment 4

> Username: mloskot  
> Created_at: 2022-04-07 11:49:13 UTC  
> Url: https://github.com/boostorg/gil/pull/640#issuecomment-1091639341  

Thank you @striezel

---
