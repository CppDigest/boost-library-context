# #1096 CircleCI increase resource_class [Merged]

> Username: sdarwin  
> Created at: 2025-10-29 23:42:44 UTC  
> Updated at: 2025-10-30 00:43:12 UTC  
> Merged at: 2025-10-30 00:43:11 UTC  
> Closed at: 2025-10-30 00:43:12 UTC  
> Url: https://github.com/boostorg/boost/pull/1096  

The job time is approaching 60 minutes and causing a timeout. There are multiple options:  
- switch to performance plan, 3 hour limit.    
- increase resource_class (which will consume credits at a faster rate).    
  
CircleCI starts at $15/month, it's not a blocker, if needed.    
  
I have not tested this change "resource_class: large" let's try it on the `develop` branch.    It may turn out 'large' is not permitted here.    
  
@pdimov

---
