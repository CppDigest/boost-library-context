# #118 Remove eUML & proto support in backmp11 [Merged]

> Username: chandryan  
> Created at: 2025-09-28 15:03:53 UTC  
> Updated at: 2025-09-28 16:42:37 UTC  
> Merged at: 2025-09-28 16:42:32 UTC  
> Closed at: 2025-09-28 16:42:32 UTC  
> Url: https://github.com/boostorg/msm/pull/118  

Remove eUML support in backmp11, as eUML is deprecated and the support in the backend requires inclusion of eUML headers and boost proto headers.  
  
Also remove the `set_states` API to get rid of the last boost proto dependency. An alternative to set states is described in the README.  
  
Related to https://github.com/boostorg/msm/issues/106

---
