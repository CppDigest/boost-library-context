# #48 handle volatile arguments to operator% by making a non-volatile copy  [Merged]

> Username: jeking3  
> Created at: 2017-10-25 16:40:27 UTC  
> Updated at: 2017-10-26 00:02:31 UTC  
> Merged at: 2017-10-26 00:02:28 UTC  
> Closed at: 2017-10-26 00:02:28 UTC  
> Url: https://github.com/boostorg/format/pull/48  

handle volatile arguments to operator% by making a non-volatile   
copy of them before feeding through  
  
Per the standard it isn't safe to cast away volatile, which is why  
making a copy is the only reasonable course of action.  
  
This fixes #36

---

## Comment 1

> Username: jeking3  
> Created_at: 2017-10-25 22:33:51 UTC  
> Url: https://github.com/boostorg/format/pull/48#issuecomment-339494643  

This passed a build on my fork.  Travis on boostorg today is under extreme duress.  
https://travis-ci.org/jeking3/format/builds/292683351  
  
Looking for a code review, please.  Thanks.

---

## Review 2 [Approved]

> Username: pdimov  
> Created_at: 2017-10-25 22:44:06 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/format/pull/48#pullrequestreview-72035409  

Looks good. You could in principle add a test for `const volatile`, although these things aren't very sensible in practice.

---

## Comment 3

> Username: jeking3  
> Created_at: 2017-10-25 23:09:25 UTC  
> Url: https://github.com/boostorg/format/pull/48#issuecomment-339501440  

@pdimov It's a good suggestion, thanks for reviewing the work.

---
