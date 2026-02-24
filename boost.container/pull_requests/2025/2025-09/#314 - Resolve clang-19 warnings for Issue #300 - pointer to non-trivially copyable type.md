# #314 Resolve clang-19 warnings for Issue #300 - pointer to non-trivially copyable type [Merged]

> Username: nigels-com  
> Created at: 2025-09-05 02:08:06 UTC  
> Updated at: 2025-09-06 10:26:31 UTC  
> Merged at: 2025-09-06 10:26:24 UTC  
> Closed at: 2025-09-06 10:26:24 UTC  
> Url: https://github.com/boostorg/container/pull/314  

In relation to Issue #300   
  
The clang-21 warning suggests casting to void pointer to resolve these warnings.  
Seems like a legitimate workaround to me.  
Tests still pass.

---

## Comment 1

> Username: nigels-com  
> Created_at: 2025-09-05 02:11:40 UTC  
> Url: https://github.com/boostorg/container/pull/314#issuecomment-3256837746  

MS Copilot says:  
  
### PR Title  
**Resolve clang-19 warnings for Issue #300 - pointer to non-trivially copyable type**    
URL: https://github.com/boostorg/container/pull/314  
  
### Description  
- Addresses issue #300 regarding warnings from clang-19/21 about pointers to non-trivially copyable types.  
- Solution: Casts pointers to `void*` as suggested by clang warnings, which acts as a legitimate workaround.  
- All tests continue to pass after the change.  
  
### Technical Summary  
- **Files changed:** 1  
- **Additions:** 2 lines  
- **Deletions:** 2 lines  
- The workaround is directly motivated by compiler diagnostics and does not affect test outcomes.  
  
### Status  
- **Branch:** `resolve-clang-19-warnings` (from your fork)  
- **Base:** `develop`  
- **Mergeable:** Yes (state: unstable, may need a rebase)  
- **Comments:** None yet  
- **Reviewers:** None requested  
  
### Review  
- The PR is small and targeted.  
- The casting workaround is a common, safe approach for silencing this class of warning and is well-justified in the PR description.  
- Since all tests pass and the change is minimal, this is a low-risk fix.  
- Consider rebasing if there are recent changes in `develop` to ensure a clean merge.  
  
**Recommendation:**    
Approve, pending any additional feedback from maintainers or further testing on different platforms.

---

## Comment 2

> Username: igaztanaga  
> Created_at: 2025-09-06 10:26:31 UTC  
> Url: https://github.com/boostorg/container/pull/314#issuecomment-3261779731  

Many thanks for the patch!

---
