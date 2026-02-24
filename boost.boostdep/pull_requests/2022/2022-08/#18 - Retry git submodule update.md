# #18 Retry git submodule update [Open]

> Username: sdarwin  
> Created at: 2022-08-16 16:08:18 UTC  
> Updated at: 2022-08-16 16:38:09 UTC  
> Url: https://github.com/boostorg/boostdep/pull/18  

There is an edge-case that occasionally happens in CI, not often. "git submodule update" fails. It reports an error "Could not resolve host: github.com". This could be a transient DNS outage or github.com server problem.  A potential fix would be to retry that step.  
Tested on python2, python3, and windows python3.  
Here is a drone run with the depinst.py -v flag enabled, to show slightly more info. (in production, -v isn't specified.)  https://drone.cpp.al/samd2/url/128

---
