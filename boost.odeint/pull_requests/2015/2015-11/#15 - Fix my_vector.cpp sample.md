# #15 Fix my_vector.cpp sample [Closed]

> Username: sehe  
> Created at: 2015-11-05 01:31:25 UTC  
> Updated at: 2015-11-05 01:51:40 UTC  
> Closed at: 2015-11-05 01:51:40 UTC  
> Url: https://github.com/boostorg/odeint/pull/15  

The my_vector type in the sample is flawed.  
  
Questionable reserve() calls in the code my have hidden the issue to date. See:  
- https://github.com/headmyshoulder/odeint-v2/issues/180  
- http://stackoverflow.com/a/33533309/85371  
  
Attached PR is the proposed fix in 2 clean commits.

---

## Comment 1

> Username: sehe  
> Created_at: 2015-11-05 01:51:40 UTC  
> Url: https://github.com/boostorg/odeint/pull/15#issuecomment-153928297  

The fix was different, see https://github.com/headmyshoulder/odeint-v2/commit/965a8e456d5e9dff47f0121a4d12aac8c845ea5e

---
