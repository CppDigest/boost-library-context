# #130 back: Revert const qualifier removals in the execute methods [Merged]

> Username: chandryan  
> Created at: 2025-10-12 15:59:41 UTC  
> Updated at: 2025-10-13 01:54:50 UTC  
> Merged at: 2025-10-13 01:54:50 UTC  
> Closed at: 2025-10-13 01:54:50 UTC  
> Url: https://github.com/boostorg/msm/pull/130  

Revert some of the changes applied in   
  
https://github.com/boostorg/msm/commit/c287cc097ea6c7afeee1c9b47e06f7671095ede8#diff-8609ca70fabeda64e1fbfe75da5348516160df6c765c137d1e4cbb301bcdae51  
  
to fix the issue described in https://github.com/boostorg/msm/issues/87  
  
TODOs:  
  
- [ ] Regression test added

---

## Comment 1

> Username: henry-ch  
> Created_at: 2025-10-12 18:18:37 UTC  
> Url: https://github.com/boostorg/msm/pull/130#issuecomment-3395185614  

Tjhe removal of const was linked with the new signature of process_event(Event&&) but it seems to do more bad than good, so we can revert this change.

---
