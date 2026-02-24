# #145 Fix update of standalone branch [Merged]

> Username: Flamefire  
> Created at: 2021-12-21 14:33:53 UTC  
> Updated at: 2021-12-21 14:41:45 UTC  
> Merged at: 2021-12-21 14:41:39 UTC  
> Closed at: 2021-12-21 14:41:39 UTC  
> Url: https://github.com/boostorg/nowide/pull/145  

Use regular rm because `git diff` ignores untracked files  
  
Also test this procedure on PRs when the HEAD commit contains "standalone"

---
