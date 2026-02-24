# #594 Test/changes in defines [Merged]

> Username: barendgehrels  
> Created at: 2019-05-29 16:55:11 UTC  
> Updated at: 2019-07-18 12:00:19 UTC  
> Merged at: 2019-06-05 08:51:26 UTC  
> Closed at: 2019-06-05 08:51:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/594  

As discussed before in issue #592 - I used Adam's suggestion, BOOST_GEOMETRY_TEST_FAILURES  
And there are a few things more in this PR

---

## Review 1 [Approved]

> Username: vissarion  
> Created_at: 2019-05-30 08:45:27 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/594#pullrequestreview-243688188  

Thanks!

---

## Review 2 [Commented]

> Username: mloskot  
> Created_at: 2019-05-30 08:49:14 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/594#pullrequestreview-243689617  

📁 include/boost/geometry/core/config.hpp

```diff
  19 | #endif
  20 | 
  21 |+ #define BOOST_GEOMETRY_USE_KRAMER_RULE
```

> Username: mloskot  
> Created_at: 2019-05-30 08:49:13 UTC  
> Updated_at: 2019-06-05 08:49:05 UTC  
> Url: https://github.com/boostorg/geometry/pull/594#discussion_r288908926  

I wonder, is this a public macro that a user is supposed to (un)define to tweak the behaviour here?  
  
If public, is this documented anywhere?

> Username: vissarion  
> Created_at: 2019-05-30 09:13:00 UTC  
> Updated_at: 2019-06-05 08:49:05 UTC  
> Url: https://github.com/boostorg/geometry/pull/594#discussion_r288916282  

It seems that this macro is not used any more and used once to bypass the validity checks on some test cases in union, but @barendgehrels should be the right person to answer.

> Username: barendgehrels  
> Created_at: 2019-06-05 08:33:53 UTC  
> Updated_at: 2019-06-05 08:49:05 UTC  
> Url: https://github.com/boostorg/geometry/pull/594#discussion_r290632236  

Sorry for the delayed answer.  
  
The macro is transitional, in the time to remove the rescaling. It selects or deselects an algorithm. It is not meant to the public (though they might use it). I will come back to it later.  
  
For now I need it because some testcases depend on its value. Therefore it is part of this PR.  
  
I will write a short comment.

> Username: mloskot  
> Created_at: 2019-06-05 08:38:59 UTC  
> Updated_at: 2019-06-05 08:49:05 UTC  
> Url: https://github.com/boostorg/geometry/pull/594#discussion_r290634197  

@barendgehrels Thanks for the explanation.


---
