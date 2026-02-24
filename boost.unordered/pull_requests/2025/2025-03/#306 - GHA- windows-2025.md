# #306 GHA: windows-2025 [Merged]

> Username: sdarwin  
> Created at: 2025-03-12 16:22:31 UTC  
> Updated at: 2025-04-16 11:04:42 UTC  
> Merged at: 2025-04-16 11:04:11 UTC  
> Closed at: 2025-04-16 11:04:11 UTC  
> Url: https://github.com/boostorg/unordered/pull/306  

Check results before any merge, it seems to generate one CI error.

---

## Comment 1

> Username: joaquintides  
> Created_at: 2025-04-14 10:05:50 UTC  
> Url: https://github.com/boostorg/unordered/pull/306#issuecomment-2801177168  

Waiting for the resolution of these two issues before proceeding:  
  
* [syntax error in `interprocess/segment_manager.hpp`](https://github.com/boostorg/interprocess/issues/258)  
* [Warning on cast from `farproc_t` in Clang 19.1 for Windows](https://github.com/boostorg/interprocess/issues/259)

---

## Comment 2

> Username: joaquintides  
> Created_at: 2025-04-14 19:52:37 UTC  
> Url: https://github.com/boostorg/unordered/pull/306#issuecomment-2802836548  

@sdarwin can you maybe push an empty commit to this PR so that CI is rerun? Some changes have been applied to Boost.Interprocess that may make this work, and I can't rerun the CI from the GHA frontend as it's too old for that (seemingly).

---

## Comment 3

> Username: joaquintides  
> Created_at: 2025-04-15 11:45:02 UTC  
> Updated_at: 2025-04-15 15:02:22 UTC  
> Url: https://github.com/boostorg/unordered/pull/306#issuecomment-2804770853  

@sdarwin I've recreated your PR in [this branch](https://github.com/boostorg/unordered/tree/feature/gha-windows-2025) so that I can rerun to my heart's content. I'm getting errors like:  
  
> The self-hosted runner: windows-2025-x64_i-050a73e0fb0bede39 lost communication with the server. Verify the machine is running and has a healthy network connection. Anything in your workflow that terminates the runner process, starves it for CPU/Memory, or blocks its network access can cause this error.  
  
(https://github.com/boostorg/unordered/actions/runs/14463577656/job/40563296995)

---

## Comment 4

> Username: joaquintides  
> Created_at: 2025-04-16 11:04:41 UTC  
> Url: https://github.com/boostorg/unordered/pull/306#issuecomment-2809231012  

Merging after a number of successful runs on a rebuilt image of windows-2025.

---
