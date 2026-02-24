# #720 ci: update actions/checkout in GitHub Actions workflows to v3 [Merged]

> Username: striezel  
> Created at: 2022-11-14 09:10:04 UTC  
> Updated at: 2022-11-14 10:38:58 UTC  
> Merged at: 2022-11-14 10:15:10 UTC  
> Closed at: 2022-11-14 10:15:10 UTC  
> Url: https://github.com/boostorg/gil/pull/720  

### Description  
  
Updates the `actions/checkout` action used in the GitHub Actions workflow to its newest major version.  
  
Changes in [actions/checkout](https://github.com/actions/checkout):  
  
> ## v3.1.0  
> - Use @actions/core `saveState` and `getState`  
> - Add `github-server-url` input  
>  
> ## v3.0.2  
> - Add input `set-safe-directory`  
>  
> ## v3.0.1  
> - Fixed an issue where checkout failed to run in container jobs due to the new git setting `safe.directory`  
> - Bumped various npm package versions  
>  
> ## v3.0.0  
>  
> - Update to node 16  
  
As far as I can tell this should all be backwards compatible, so I do not expect any breakage.  
  
### Tasklist  
  
- [x] Ensure all CI builds pass  
- [x] Review and approve

---

## Review 1 [Approved]

> Username: mloskot  
> Created_at: 2022-11-14 10:14:59 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/720#pullrequestreview-1178791040  

Thank you!

---
