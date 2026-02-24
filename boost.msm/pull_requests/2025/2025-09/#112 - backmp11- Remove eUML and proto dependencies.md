# #112 backmp11: Remove eUML and proto dependencies [Closed]

> Username: chandryan  
> Created at: 2025-09-20 07:15:49 UTC  
> Updated at: 2025-10-06 18:16:03 UTC  
> Closed at: 2025-09-27 16:06:07 UTC  
> Url: https://github.com/boostorg/msm/pull/112  

Applied changes for backmp11:  
Removed suppport for eUML and other proto dependencies (https://github.com/boostorg/msm/issues/106), which includes the `set_states` API as well as copying states into the SM in the backend constructor.   
Added a rationale for the removal of them in the temp README under the backmp11 include dir.  
Added a Backmp11 version of TestConstructor.cpp to test the adapted constructor and to make sure states can still be copied by using `get_state`.  
  
The removal leads to about ~0.5s less compile time and ~20MB less RAM usage.  
  
This PR also removes some of the test failures mentioned in https://github.com/boostorg/msm/issues/105

---

## Comment 1

> Username: chandryan  
> Created_at: 2025-09-27 16:06:07 UTC  
> Url: https://github.com/boostorg/msm/pull/112#issuecomment-3341884286  

Reflecting on this PR, I'm not sure whether it really is a good idea to start introducing backwards-incompatible changes in backmp11; even for removing features which are likely to be negligible.  
  
It is going to be confusing to understand which backend supports what, and these distinctions will likely convolute the code base as well the documentation.  
  
For the moment I will collect backwards-incompatible on a separate branch in my fork, we can evaluate later if we want to go forward with introducing backwards-incompatible changes or discuss alternatives.

---
