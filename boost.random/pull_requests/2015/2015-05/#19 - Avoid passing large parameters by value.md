# #19 Avoid passing large parameters by value [Closed]

> Username: tempoz  
> Created at: 2015-05-28 20:50:25 UTC  
> Updated at: 2018-01-03 00:29:31 UTC  
> Closed at: 2018-01-03 00:29:31 UTC  
> Url: https://github.com/boostorg/random/pull/19  

Both the Engine and Distribution may be passed by const reference, which is best practice in this case to improve performance.  
This was uncovered by Coverity and is related to defect CID72373.

---

## Comment 1

> Username: swatanabe  
> Created_at: 2018-01-03 00:29:31 UTC  
> Url: https://github.com/boostorg/random/pull/19#issuecomment-354912140  

This change is invalid in C++03, as Engine may already be a reference type.  variate_generator is mostly obsolete anyway.

---
