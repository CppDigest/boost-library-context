# #10 Avoid potential conflict between AIX's and Lambda's var. [Merged]

> Username: ismirlian  
> Created at: 2014-07-30 14:25:57 UTC  
> Updated at: 2014-07-30 15:11:38 UTC  
> Merged at: 2014-07-30 15:05:25 UTC  
> Closed at: 2014-07-30 15:05:25 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/10  

In ../boost/lambda/detail/lambda_functor_base.hpp there is variable called var that conflicts with an AIX system variable. The entire file (sched.h) does not need to be included only the one function it uses (sched_yield).

---

## Comment 1

> Username: pdimov  
> Created_at: 2014-07-30 15:05:49 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/10#issuecomment-50627501  

Applied, thank you for the patience.

---

## Comment 2

> Username: ismirlian  
> Created_at: 2014-07-30 15:11:38 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/10#issuecomment-50628336  

Oh no problem. Writing a good commit message is important, and it's something I don't have much experience with yet.

---
