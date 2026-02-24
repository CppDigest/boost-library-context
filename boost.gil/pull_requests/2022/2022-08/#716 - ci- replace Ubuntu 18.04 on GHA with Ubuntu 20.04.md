# #716 ci: replace Ubuntu 18.04 on GHA with Ubuntu 20.04 [Merged]

> Username: striezel  
> Created at: 2022-08-09 16:06:04 UTC  
> Updated at: 2022-08-10 19:15:03 UTC  
> Merged at: 2022-08-10 16:37:21 UTC  
> Closed at: 2022-08-10 16:37:21 UTC  
> Url: https://github.com/boostorg/gil/pull/716  

### Description  
  
GitHub started the deprecation process for Ubuntu 18.04 Action runners on 2022-08-08, and Ubuntu 18.04 will be completely unsupported by 2022-12-01 (if things proceed as planned).  
  
So this PR tries to replace all Ubuntu 18.04 runners with the next newest Ubuntu 20.04 runner. I don't think this will work on first try, but we need to start somewhere.  
  
So this commit replace those Ubuntu 18.04 jobs with the next newest Ubuntu 20.04 runner that have shown to still work with the newer version. The other five GHA jobs with Ubuntu 18.04 (gcc-6, gcc-7, clang++-3.9, clang++-4.0, clang++-5.0) still need to be dealt with separately later.  
  
### References  
  
See <https://github.blog/changelog/2022-08-09-github-actions-the-ubuntu-18-04-actions-runner-image-is-being-deprecated-and-will-be-removed-by-12-1-22/> for the deprecation announcement and more information on that.  
  
### Tasklist  
  
- [x] Ensure all CI builds pass  
- [x] Review and approve

---

## Comment 1

> Username: striezel  
> Created_at: 2022-08-09 16:18:48 UTC  
> Updated_at: 2022-08-09 16:41:49 UTC  
> Url: https://github.com/boostorg/gil/pull/716#issuecomment-1209595747  

_(Note for myself)_ Status so far:  
  
Failed jobs due to compiler package not being available on Ubuntu 20.04:  
* gcc-6  
* gcc-7  
* clang++-3.9  
* clang++-4.0  
* clang++-5.0  
  
That's five out of the ten changed build jobs. The other jobs may possibly run just fine on Ubuntu 20.04, and if those succeed, then I may possibly update this PR for those remaining jobs that succeeded.  
  
The MacOS 10.15 failure on Azure Pipelines is unrelated. It happened due to a [scheduled "brownout" of MacOS 10.15](https://github.com/actions/runner-images/issues/5583).

---

## Comment 2

> Username: striezel  
> Created_at: 2022-08-09 17:32:56 UTC  
> Url: https://github.com/boostorg/gil/pull/716#issuecomment-1209667618  

@mloskot:  
This PR is now ready for review.

---

## Review 3 [Approved]

> Username: mloskot  
> Created_at: 2022-08-10 16:36:57 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/716#pullrequestreview-1068604578  

Awesome, thank you @striezel !

---
