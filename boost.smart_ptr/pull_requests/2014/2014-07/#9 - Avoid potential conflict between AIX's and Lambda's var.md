# #9 Avoid potential conflict between AIX's and Lambda's var. [Closed]

> Username: ismirlian  
> Created at: 2014-07-29 17:12:40 UTC  
> Updated at: 2014-07-30 14:11:34 UTC  
> Closed at: 2014-07-30 14:11:34 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/9  

In ../boost/lambda/detail/lambda_functor_base.hpp there is variable called var that conflicts with an AIX system variable. The entire file (sched.h) does not need to be included only the one function it uses (sched_yield).

---

## Comment 1

> Username: glenfe  
> Created_at: 2014-07-29 18:58:17 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/9#issuecomment-50521951  

The commit message for this should really not start with "Reworked solution after taking to maintainer, He said...<et cetera>".

---

## Comment 2

> Username: pdimov  
> Created_at: 2014-07-29 20:01:06 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/9#issuecomment-50530077  

Glen is right, the commit message would make no sense in the log, outside of context. It should really be something like "Avoid potential conflict between AIX's <var.h> and Lambda's var."

---

## Comment 3

> Username: ismirlian  
> Created_at: 2014-07-30 13:53:09 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/9#issuecomment-50616882  

Alright does the message look better? I still need to recommit it though.

---
