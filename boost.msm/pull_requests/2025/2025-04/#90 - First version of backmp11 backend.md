# #90 First version of backmp11 backend [Closed]

> Username: chandryan  
> Created at: 2025-04-15 11:26:30 UTC  
> Updated at: 2025-04-15 11:27:56 UTC  
> Closed at: 2025-04-15 11:27:55 UTC  
> Url: https://github.com/boostorg/msm/pull/90  

First version of the backmp11 backend.  
  
Includes the following changes (more details are described in include/boost/msm/backmp11/README.md):  
- Backend `backmp11` available in `boost::msm::backmp11` namespace  
  - In the policy `favor_runtime_speed` all resource-intensive usages of MPL are replaced with Mp11  
  - In the policy `favor_compile_time` a new mechanism for dispatch tables has been implemented  
  
Open points to be addressed in another PR:  
- bench folder needs to be removed  
- Documentation needs to be updated

---

## Comment 1

> Username: chandryan  
> Created_at: 2025-04-15 11:27:55 UTC  
> Updated_at: 2025-04-15 11:27:56 UTC  
> Url: https://github.com/boostorg/msm/pull/90#issuecomment-2804727719  

Woops sorry, wrong target repo 😅

---
