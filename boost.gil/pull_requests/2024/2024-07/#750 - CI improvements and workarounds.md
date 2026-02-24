# #750 CI improvements and workarounds [Merged]

> Username: striezel  
> Created at: 2024-07-04 21:56:12 UTC  
> Updated at: 2025-08-03 18:08:22 UTC  
> Merged at: 2024-07-04 22:56:34 UTC  
> Closed at: 2024-07-04 22:56:34 UTC  
> Url: https://github.com/boostorg/gil/pull/750  

### Description  
  
This pull request attempts to fix two issues with the CI jobs:  
* failure of container-based jobs seen in https://github.com/boostorg/gil/actions/runs/9796328751  
* removal of macOS 11 from the available runner images (discussed earlier here: https://github.com/boostorg/gil/pull/749#issuecomment-2207435037)  
  
#### Failure of container jobs  
  
Node.js 20 is not supported in older containers like `ubuntu:18.04`, but GitHub Actions nowawadays forces the use of Node.js 20, even if the action itself still specified Node.js 16 as the version to use. The result is that e.g. `actions/checkout@v3` fails in the containers, although it should not (because v3 is still specified Node.js 16).  See <https://github.blog/changelog/2024-03-07-github-actions-all-actions-will-run-on-node20-instead-of-node16-by-default/> for more information on that forced Node.js version. Fortunately, there still is a workaround to restore the old behaviour, and that is to set a environment variable to allow older Node.js to be used in those actions, and that is what the first commit does.  
  
However, this workaround may only work for a limited time, because Node.js 16 will most likely be removed in October 2024. Then we may have to figure out a different way to test older compilers.  
  
#### macOS 11 no longer available  
  
The runner image for macOS 11 has been removed on 2024-06-28. See <https://github.blog/changelog/2024-05-20-actions-upcoming-changes-to-github-hosted-macos-runners/> for more information on that.  
  
So the easiest fix is probably just to move to the next newest version of macOS that is still avaiable for GHA, and that is macOS 12.  
  
### References  
  
<!-- Any links related to this PR: issues, other PRs, mailing list threads, StackOverflow questions, etc. -->  
* Removal of macOS 11 runner image: https://github.blog/changelog/2024-05-20-actions-upcoming-changes-to-github-hosted-macos-runners/  
* Forced use of Node.js 20 for all jobs: https://github.blog/changelog/2024-05-17-updated-dates-for-actions-runner-using-node20-instead-of-node16-by-default/  
  
### Tasklist  
  
- [x] Ensure all CI builds pass  
- [x] Review and approve

---

## Review 1 [Approved]

> Username: mloskot  
> Created_at: 2024-07-04 22:56:20 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/750#pullrequestreview-2159553065  

Thank you so much @striezel !

---
