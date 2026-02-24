# #31 Allow object_pool::construct() to use variadic template, if available. [Merged]

> Username: jbherdman  
> Created at: 2019-09-06 05:43:13 UTC  
> Updated at: 2025-07-03 17:13:24 UTC  
> Merged at: 2025-07-03 17:13:24 UTC  
> Closed at: 2025-07-03 17:13:24 UTC  
> Url: https://github.com/boostorg/pool/pull/31  

Fixes issue #22, with a fallback to the existing code for compilers that don't support variadic-templates.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2019-09-06 06:28:52 UTC  
> Updated_at: 2022-08-04 10:03:03 UTC  
> Url: https://github.com/boostorg/pool/pull/31#issuecomment-528728767  

# [Codecov](https://codecov.io/gh/boostorg/pool/pull/31?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#31](https://codecov.io/gh/boostorg/pool/pull/31?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (600bcb0) into [develop](https://codecov.io/gh/boostorg/pool/commit/600bcb027379b0670ccecf14f380f77e1264037f?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (600bcb0) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
> :exclamation: Current head 600bcb0 differs from pull request most recent head 324c7ea. Consider uploading reports for the commit 324c7ea to get more accurate results  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/pool/pull/31/graphs/tree.svg?width=650&height=150&src=pr&token=blmk8CZxg2&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/pool/pull/31?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop      #31   +/-   ##  
========================================  
  Coverage    93.48%   93.48%             
========================================  
  Files            9        9             
  Lines          583      583             
========================================  
  Hits           545      545             
  Misses          38       38             
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/pool/pull/31?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/pool/pull/31?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [600bcb0...324c7ea](https://codecov.io/gh/boostorg/pool/pull/31?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).

---

## Comment 2

> Username: jbherdman  
> Created_at: 2019-09-19 02:47:32 UTC  
> Url: https://github.com/boostorg/pool/pull/31#issuecomment-532942141  

The first commit of this PR (7f9a0ba) should be fine, but it failed the code-coverage check.  After deeper investigation, I don't see why lines 186 + 187 of the (modified) 'object_pool.hpp' are marked as yellow/partial coverage.  The unit tests should already be properly covering both the case where the constructor throws, and the case where it does not.  
  
For the second commit (311661f), I decided to try adding a simple unit-test where the UserAllocator "malloc" would fail (return zero), just to increase the code coverage.  However, that fails the valgrind build/test for some reason that doesn't make sense to me.  The error-message indicates that the 'cdtor_checker::check_out()' is failing, but in the lines that I added, I don't see where the "tester" object is ever being constructed.  The always-fails "malloc()" should be preventing the placement-new from ever being run, so I'm not sure where the "tester" object being destructed is coming from.  
  
I'm at just about the limit of what I can reasonably test here, as I am doing my development on Windows and don't have local access to valgrind.  I'm also relatively new to github, so I'm not sure whether it is easy for you to pick up the "first half" of this PR, or whether it would be easier if I did something differently on my end.  Please advise.

---

## Comment 3

> Username: jeking3  
> Created_at: 2022-05-03 00:42:53 UTC  
> Url: https://github.com/boostorg/pool/pull/31#issuecomment-1115504910  

@jbherdman please rebase on develop for a more complete CI suite

---

## Comment 4

> Username: fschoenberger  
> Created_at: 2022-06-16 13:54:12 UTC  
> Url: https://github.com/boostorg/pool/pull/31#issuecomment-1157689497  

Hi I just wanted to check if this pull request is going to get merged or - if not - I could somehow help getting it merged? For my projects it would be incredibly useful.

---

## Comment 5

> Username: jbherdman  
> Created_at: 2022-06-17 03:18:14 UTC  
> Url: https://github.com/boostorg/pool/pull/31#issuecomment-1158446986  

Sorry for the delay.  Rebased to the latest develop, and it looks like it passes the CI checks now.  
  
If there is anything more that needs to be done on my end, just let me know.

---

## Comment 6

> Username: fschoenberger  
> Created_at: 2022-07-05 15:29:49 UTC  
> Url: https://github.com/boostorg/pool/pull/31#issuecomment-1175195428  

@jeking3 From your point of view, is there anything else to do? I would love to see this PR get merged.   
  
I appreciate that maintaining open source is really hard work and I don't want to be annoying. If there's something left to do, I'd be happy to do it.

---

## Comment 7

> Username: jeking3  
> Created_at: 2022-07-06 21:47:33 UTC  
> Url: https://github.com/boostorg/pool/pull/31#issuecomment-1176783220  

Thanks for rebasing; looks like we have some issues to deal with identified by CI.  There are some untested branches as well as a valgrind error (invalid write) which was not there before.

---

## Comment 8

> Username: jbherdman  
> Created_at: 2022-07-07 02:33:28 UTC  
> Url: https://github.com/boostorg/pool/pull/31#issuecomment-1176975959  

@jeking3 Looking at the logs, the valgrind error (invalid write) is only triggered by the pre-existing `test_valgrind_fail_2.cpp`.  
  
That appears to be an intentional failure-case (intentional write to released memory).  I don't know why CI did not catch that issue before, but I see no reason why that would be a new failure under this PR.

---

## Comment 9

> Username: jbherdman  
> Created_at: 2022-07-07 03:42:16 UTC  
> Url: https://github.com/boostorg/pool/pull/31#issuecomment-1177018438  

@jeking3 I believe the valgrind error (invalid write) is a red herring.  Reading through the logs, I only see those lines as appearing with a "(failed-as-expected)" line afterwards.  
  
The valgrind failure might be coming from this line:  
`test 'objs.find(This) != objs.end()' failed in function 'void cdtor_checker::check_out(void *const)'`  
  
However, the only way I can see that failing would be if the destructor is being called with `this == NULL`.  I have added an extra assert to try to highlight that case/issue in CI, as I have no local mechanism to run valgrind.  
  
I have also added additional coverage-tests for the case where the variadic-template constructor fails by throwing an exception.

---

## Comment 10

> Username: jbherdman  
> Created_at: 2022-07-07 19:46:43 UTC  
> Url: https://github.com/boostorg/pool/pull/31#issuecomment-1178143241  

@jeking3 I found and fixed the problem that was causing the valgrind issue.  
  
The special `BOOST_POOL_VALGRIND` version of the code was not behaving correctly when faced with a user-allocator that returned `NULL` (as added to enhance coverage testing).  It was adding the `NULL` to its internal list of allocated-blocks, leading to the issues later on with invalid destructor calls, etc.  
  
As for the Codecov report, it seems to only be looking at the first commit in this PR?  I'm not sure how to fix that, shy of, I suppose:  deleting my fork, re-forking, making all the changes in a single commit, and then opening a new PR?  There must be an easier way, but I don't use git/github very often.

---

## Comment 11

> Username: jbherdman  
> Created_at: 2022-07-12 21:37:36 UTC  
> Url: https://github.com/boostorg/pool/pull/31#issuecomment-1182523774  

@fschoenberger Maybe a dumb idea on my part, but did you want to make your own fork, include my changes (all in a single commit), and submit it as new PR?  That should get proper code-coverage numbers, without me needing to poke around and figure out how to 'squash' the commits on my end.  I seem to have done something wrong with mixing rebases + merges, and GitHub Desktop is just not letting me do what I want to do.  (I don't care about getting "credit" or whatever for the changes, I just want the enhancements to make their way into Boost.)

---

## Comment 12

> Username: fschoenberger  
> Created_at: 2022-07-12 21:54:11 UTC  
> Updated_at: 2022-07-12 21:54:23 UTC  
> Url: https://github.com/boostorg/pool/pull/31#issuecomment-1182535474  

> @fschoenberger Maybe a dumb idea on my part, but did you want to make your own fork, include my changes (all in a single commit), and submit it as new PR? That should get proper code-coverage numbers, without me needing to poke around and figure out how to 'squash' the commits on my end. [...]  
  
@jbherdman Sure, I have create a new Pull Request.

---

## Review 13 [Approved]

> Username: jeking3  
> Created_at: 2025-07-03 17:03:47 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/pool/pull/31#pullrequestreview-2983940199  

I verified the post-conditions are the same for both compile-time branches.

---
