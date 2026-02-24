# #54 Included boost/throw_exception.hpp when exceptions are disabled. [Closed]

> Username: dgrafov  
> Created at: 2017-06-01 09:58:25 UTC  
> Updated at: 2017-06-06 14:01:44 UTC  
> Closed at: 2017-06-06 14:01:44 UTC  
> Url: https://github.com/boostorg/serialization/pull/54  

There is a compilation error when boost is compiled without exceptions: boost::serialization::throw_exception calls boost::throw_exception which is not declared in this case. The declaration is situated in boost/throw_exception.hpp.

---

## Review 1 [Commented]

> Username: robertramey  
> Created_at: 2017-06-04 18:33:42 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/serialization/pull/54#pullrequestreview-41947968  

What is the motivation for this?  The serialization library has take great pains to avoid sucking in a huge amount of overhead which boost::exception entails.  Do things not compile without exceptions enabled now?

---

## Comment 2

> Username: dgrafov  
> Created_at: 2017-06-06 14:01:44 UTC  
> Url: https://github.com/boostorg/serialization/pull/54#issuecomment-306495496  

@robertramey it gives a compiler error when I use binary_from_base64 without exception and compiler points to this file, so it looked reasonable to me, that if boost::throw_exception is called, the file with it's declaration should be included. But now I see, that there is no include by intent and I can fix the compilation error in my own code. So I'm closing my pull-request.

---
