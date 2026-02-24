# #12 Bugfix/qi tests [Merged]

> Username: vtnerd  
> Created at: 2014-04-16 12:10:59 UTC  
> Updated at: 2014-04-17 00:54:29 UTC  
> Merged at: 2014-04-16 23:47:50 UTC  
> Closed at: 2014-04-16 23:47:50 UTC  
> Url: https://github.com/boostorg/spirit/pull/12  

This fixes all of the unit tests for qi, and has some additional cleanup. Combined with the boost::phoenix patch, all of the qi and qi_regression tests compile and pass.

---

## Comment 1

> Username: djowel  
> Created_at: 2014-04-16 13:27:36 UTC  
> Updated_at: 2014-04-16 13:28:15 UTC  
> Url: https://github.com/boostorg/spirit/pull/12#issuecomment-40598086  

There shouldn't be anything wrong with the alternative test code. What's wrong with it? It should be working as designed.

---

## Comment 2

> Username: vtnerd  
> Created_at: 2014-04-16 17:07:00 UTC  
> Url: https://github.com/boostorg/spirit/pull/12#issuecomment-40625038  

These changes are for v2 not the x3 branch. The history shows that this file changed with a commit for x3 - was the wrong test file modified in that commit?  
  
After looking at action_dispatch.hpp for v2, I think the callback argument detection only works for function pointers when using a c++03 compiler.  
  
After seeing the history it looks like the action functor should be:  
  
``` c++  
operator()(result_type const& v, unused_type, unused_type)  
```  
  
Is that true?

---

## Comment 3

> Username: djowel  
> Created_at: 2014-04-16 23:47:42 UTC  
> Url: https://github.com/boostorg/spirit/pull/12#issuecomment-40666121  

woa! you are right! this is wrong commit!

---

## Comment 4

> Username: djowel  
> Created_at: 2014-04-16 23:48:52 UTC  
> Url: https://github.com/boostorg/spirit/pull/12#issuecomment-40666194  

Thanks for spotting this, Lee!

---
