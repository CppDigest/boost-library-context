# #613 Feature/changes for selfturns and tests [Merged]

> Username: barendgehrels  
> Created at: 2019-07-17 15:01:50 UTC  
> Updated at: 2019-08-10 13:17:38 UTC  
> Merged at: 2019-08-10 13:17:30 UTC  
> Closed at: 2019-08-10 13:17:30 UTC  
> Url: https://github.com/boostorg/geometry/pull/613  

Some minor changes in code, more changes in some tests

---

## Review 1 [Commented]

> Username: barendgehrels  
> Created_at: 2019-07-17 15:02:57 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/613#pullrequestreview-263117947  

📁 test/algorithms/check_validity.hpp

```diff
   8 |+ 
   9 |+ #ifndef BOOST_GEOMETRY_TEST_CHECK_VALIDITY_HPP
  10 |+ #define BOOST_GEOMETRY_TEST_CHECK_VALIDITY_HPP
```

> Username: barendgehrels  
> Created_at: 2019-07-17 15:02:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/613#discussion_r304457737  

Note, this file is moved, it is adapted but not new


---

## Review 2 [Approved]

> Username: vissarion  
> Created_at: 2019-07-31 07:51:42 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/613#pullrequestreview-268838644  

Sorry for late review. It looks  OK to me. Thanks!

---

## Comment 3

> Username: barendgehrels  
> Created_at: 2019-07-31 19:37:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/613#issuecomment-516990781  

> Sorry for late review. It looks OK to me. Thanks!  
  
Thank you!

---

## Comment 4

> Username: barendgehrels  
> Created_at: 2019-08-07 09:11:56 UTC  
> Url: https://github.com/boostorg/geometry/pull/613#issuecomment-519013091  

@awulkiew is it OK if I merge this? My next PR is dependent on this one.

---

## Review 5 [Commented]

> Username: awulkiew  
> Created_at: 2019-08-08 12:38:23 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/613#pullrequestreview-272540383  

📁 extensions/test/algorithms/dissolve.cpp

```diff
 654 |+ #if defined(BOOST_GEOMETRY_TEST_FAILURES)
 655 |     TEST_DISSOLVE(gitter_2013_04_a, 3043.9181, 3, 0, 21);
 656 |+ #endif
```

> Username: awulkiew  
> Created_at: 2019-08-08 12:38:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/613#discussion_r312011316  

Is this a regression in dissolve introduced by this PR?

> Username: barendgehrels  
> Created_at: 2019-08-08 17:52:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/613#discussion_r312165529  

Not that I know of. It did not compile before, so I did not bisect when it started failing


---

## Review 6 [Commented]

> Username: awulkiew  
> Created_at: 2019-08-08 12:43:31 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/613#pullrequestreview-272543271  

📁 include/boost/geometry/algorithms/detail/is_valid/has_valid_self_turns.hpp

```diff
  94 |-                                           interrupt_policy);
  95 |+                                           interrupt_policy,
  96 |+                                           0, true);
```

> Username: awulkiew  
> Created_at: 2019-08-08 12:43:31 UTC  
> Updated_at: 2019-08-08 12:49:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/613#discussion_r312013644  

What does this change do? Does this change validity check WRT how it was in the past or is it a change needed because some other change was done in `self_turns`?  
  
What are adjacent turns (or segments?) and why should they be skipped when checking validity?

> Username: awulkiew  
> Created_at: 2019-08-08 12:53:41 UTC  
> Url: https://github.com/boostorg/geometry/pull/613#discussion_r312018167  

According to this comment:   
  
    // In some cases (dissolve, has_self_intersections)  
    // neighbouring segments should be checked  
    // (for example to detect spikes properly)  
  
(https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/algorithms/detail/overlay/get_turns.hpp#L393)  
  
Though in `is_valid` spikes are detected separately here:  
  
https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/algorithms/detail/is_valid/ring.hpp#L210  
  
May these adjacent turns be important for some other reason?

> Username: barendgehrels  
> Created_at: 2019-08-08 17:52:23 UTC  
> Updated_at: 2019-08-08 17:52:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/613#discussion_r312165620  

Thanks, I will verify it.

> Username: barendgehrels  
> Created_at: 2019-08-10 09:11:14 UTC  
> Updated_at: 2019-08-10 09:11:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/613#discussion_r312696718  

It is indeed wrong, I will revert it. Strangely enough the tests in `is_valid` did not catch it, so I will add a testcase there too.  
  
It causes some false positives in my branch so I have to solve that in another way.  
  
Will update this PR today. Thanks for the catch!

> Username: barendgehrels  
> Created_at: 2019-08-10 09:55:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/613#discussion_r312697775  

Hmm, that was in my derived branch. In the original branch it is all OK, and also well tested.  
  
Indeed, spikes are detected separately and therefore adjacent turns to detect turns in validity checking. So I think we can apply it as is now.  
  
Agree?

> Username: awulkiew  
> Created_at: 2019-08-10 12:31:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/613#discussion_r312701433  

Ok, if there are problems in the future we can revert it later.

> Username: barendgehrels  
> Created_at: 2019-08-10 13:17:05 UTC  
> Url: https://github.com/boostorg/geometry/pull/613#discussion_r312702581  

Thank you, and a bug-label is OK


---

## Comment 7

> Username: awulkiew  
> Created_at: 2019-08-10 12:35:13 UTC  
> Url: https://github.com/boostorg/geometry/pull/613#issuecomment-520145533  

I marked it as a bug fix because of dissolve but if you think it's more like an enhancement needed by your future work please change it.

---
