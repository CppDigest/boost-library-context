# #457 Fix several typos [Merged]

> Username: striezel  
> Created at: 2025-01-20 23:48:17 UTC  
> Updated at: 2025-01-22 23:55:34 UTC  
> Merged at: 2025-01-22 23:45:17 UTC  
> Closed at: 2025-01-22 23:45:17 UTC  
> Url: https://github.com/boostorg/process/pull/457  

Fixes several typos, no functional changes intended.

---

## Comment 1

> Username: striezel  
> Created_at: 2025-01-22 22:07:23 UTC  
> Url: https://github.com/boostorg/process/pull/457#issuecomment-2608370315  

I am not sure why the Windows builds are failing here. However, they seem to fail on the develop branch, too (see https://github.com/boostorg/process/actions/runs/12738550271 for example). So my guess is that these build failures are not related to the changes in this pull request. @klemens-morgenstern, perhaps you can shed some light on this and tell whether that guess is wrong or right?

---

## Comment 2

> Username: klemens-morgenstern  
> Created_at: 2025-01-22 23:44:45 UTC  
> Url: https://github.com/boostorg/process/pull/457#issuecomment-2608493809  

Something broke env access at some point for some reason. Gha is fun.  
  
Windows is tested on drone, so this PR can ignore that

---

## Comment 3

> Username: striezel  
> Created_at: 2025-01-22 23:47:39 UTC  
> Url: https://github.com/boostorg/process/pull/457#issuecomment-2608496670  

> Something broke env access at some point for some reason. Gha is fun.  
  
Thank you for the clarification.

---
