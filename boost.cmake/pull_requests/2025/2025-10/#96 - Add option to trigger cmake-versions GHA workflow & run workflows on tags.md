# #96 Add option to trigger cmake-versions GHA workflow & run workflows on tags [Merged]

> Username: Flamefire  
> Created at: 2025-10-10 12:58:41 UTC  
> Updated at: 2025-10-11 13:20:13 UTC  
> Merged at: 2025-10-11 09:25:53 UTC  
> Closed at: 2025-10-11 09:25:53 UTC  
> Url: https://github.com/boostorg/cmake/pull/96  

This will run especially for beta releases and can be used to trigger the CMake version checks/smoke-test prior to that or for testing out individual changes introduced by libraries.

---

## Review 1 [Commented]

> Username: pdimov  
> Created_at: 2025-10-10 13:07:37 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/cmake/pull/96#pullrequestreview-3323353371  

📁 .github/workflows/cmake_versions.yml

```diff
  35 |         run: |
  36 |           REF=${GITHUB_BASE_REF:-$GITHUB_REF}
  37 |           REF=${REF#refs/heads/}
```

> Username: pdimov  
> Created_at: 2025-10-10 13:07:37 UTC  
> Url: https://github.com/boostorg/cmake/pull/96#discussion_r2420085963  

These two lines should probably be removed?


---

## Comment 2

> Username: pdimov  
> Created_at: 2025-10-11 13:10:35 UTC  
> Url: https://github.com/boostorg/cmake/pull/96#issuecomment-3393316659  

```  
fatal: Remote branch auto not found in upstream origin  
```

---

## Comment 3

> Username: Flamefire  
> Created_at: 2025-10-11 13:20:13 UTC  
> Url: https://github.com/boostorg/cmake/pull/96#issuecomment-3393324654  

Forgot about that option that I added after writing that part of the code...  
https://github.com/boostorg/cmake/pull/97

---
