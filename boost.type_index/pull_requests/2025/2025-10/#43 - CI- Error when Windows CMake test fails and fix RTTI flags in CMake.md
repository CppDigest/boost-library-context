# #43 CI: Error when Windows CMake test fails and fix RTTI flags in CMake [Merged]

> Username: Flamefire  
> Created at: 2025-10-09 17:55:52 UTC  
> Updated at: 2025-10-14 10:55:11 UTC  
> Merged at: 2025-10-14 09:58:35 UTC  
> Closed at: 2025-10-14 09:58:35 UTC  
> Url: https://github.com/boostorg/type_index/pull/43  

Only the exit code of the last command is considered, so make sure CTest is last  
  
See this failing test with success on CI: https://github.com/boostorg/type_index/actions/runs/18352125278/job/52274666284#step:4:265  
  
Also `-fno-rtti` was used in CMake for Windows too instead of the variable.  
  
Also check why that link test fails (because linking succeeds). I'm not sure how it is intended to fail so can't fix it.

---

## Comment 1

> Username: apolukhin  
> Created_at: 2025-10-14 09:59:04 UTC  
> Url: https://github.com/boostorg/type_index/pull/43#issuecomment-3401041652  

Many thanks!  
  
Merged the PR, will take care of the link fail in a separate PR

---
