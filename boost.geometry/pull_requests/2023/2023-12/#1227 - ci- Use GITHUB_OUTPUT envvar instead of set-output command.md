# #1227 ci: Use GITHUB_OUTPUT envvar instead of set-output command [Merged]

> Username: arunsathiya  
> Created at: 2023-12-30 19:54:09 UTC  
> Updated at: 2024-01-26 06:59:08 UTC  
> Merged at: 2024-01-26 06:59:07 UTC  
> Closed at: 2024-01-26 06:59:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/1227  

`save-state` and `set-output` commands used in GitHub Actions are deprecated and [GitHub recommends using environment files](https://github.blog/changelog/2023-07-24-github-actions-update-on-save-state-and-set-output-commands/).  
  
This PR updates the usage of `set-output` to `$GITHUB_OUTPUT`  
  
Instructions for envvar usage from GitHub docs:  
  
https://docs.github.com/en/actions/using-workflows/workflow-commands-for-github-actions#setting-an-output-parameter

---

## Review 1 [Approved]

> Username: vissarion  
> Created_at: 2024-01-09 10:25:15 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1227#pullrequestreview-1810850278  

Thanks! LGTM.

---

## Review 2 [Approved]

> Username: mloskot  
> Created_at: 2024-01-23 09:24:49 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1227#pullrequestreview-1838268342  

It's always good to catch up with changes on the CI services indeed

---
