# #86 Use nullptr instead of 0 if possible [Merged]

> Username: sjoubert  
> Created at: 2018-09-05 08:42:18 UTC  
> Updated at: 2018-10-11 12:49:38 UTC  
> Merged at: 2018-09-06 11:22:35 UTC  
> Closed at: 2018-09-06 11:22:36 UTC  
> Url: https://github.com/boostorg/date_time/pull/86  

This is to prevent gcc's -Wzero-as-nullptr-constant warnings

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2018-09-05 10:36:10 UTC  
> Url: https://github.com/boostorg/date_time/pull/86#issuecomment-418683318  

# [Codecov](https://codecov.io/gh/boostorg/date_time/pull/86?src=pr&el=h1) Report  
> Merging [#86](https://codecov.io/gh/boostorg/date_time/pull/86?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/date_time/commit/66ee8bf48fbec95ae4dfa59a128b3b3305261455?src=pr&el=desc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/date_time/pull/86/graphs/tree.svg?width=650&token=nDoh7t8f6g&height=150&src=pr)](https://codecov.io/gh/boostorg/date_time/pull/86?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop      #86   +/-   ##  
========================================  
  Coverage    51.48%   51.48%             
========================================  
  Files           83       83             
  Lines         4984     4984             
  Branches      2441     2441             
========================================  
  Hits          2566     2566             
  Misses         403      403             
  Partials      2015     2015  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/date_time/pull/86?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [...boost/date\_time/posix\_time/posix\_time\_duration.hpp](https://codecov.io/gh/boostorg/date_time/pull/86/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9kYXRlX3RpbWUvcG9zaXhfdGltZS9wb3NpeF90aW1lX2R1cmF0aW9uLmhwcA==) | `100% <ø> (ø)` | :arrow_up: |  
| [include/boost/date\_time/time\_duration.hpp](https://codecov.io/gh/boostorg/date_time/pull/86/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9kYXRlX3RpbWUvdGltZV9kdXJhdGlvbi5ocHA=) | `82.41% <ø> (ø)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/date_time/pull/86?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/date_time/pull/86?src=pr&el=footer). Last update [66ee8bf...3e24790](https://codecov.io/gh/boostorg/date_time/pull/86?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: mclow  
> Created_at: 2018-09-05 13:26:14 UTC  
> Url: https://github.com/boostorg/date_time/pull/86#issuecomment-418729646  

This seems like the kind of thing that would be useful boost-wide, not just in date-time.

---

## Comment 3

> Username: JeffGarland  
> Created_at: 2018-09-05 14:11:05 UTC  
> Url: https://github.com/boostorg/date_time/pull/86#issuecomment-418745394  

Yeah, I don't know I have mixed feelings on the subject since it's more code uglification via macro.  At one level I'm ready to cut the cord on 98 and require c++11 so this can just be written normally...

---

## Comment 4

> Username: mclow  
> Created_at: 2018-09-05 17:06:29 UTC  
> Url: https://github.com/boostorg/date_time/pull/86#issuecomment-418806983  

`BOOST_DATE_TIME_NULLPTR` is certainly much harder to type than `0`

---

## Comment 5

> Username: jeking3  
> Created_at: 2018-09-06 11:21:05 UTC  
> Url: https://github.com/boostorg/date_time/pull/86#issuecomment-419058405  

@JeffGarland there was a discussion on the mailing list about officially dropping C++03 and earlier support, with the consensus that if one still needed C++03 support that they would be able to continue to use older versions of boost; and in fact some libraries (like Boost.Move, which exists solely for the transition) would still need that support, but the project as a whole would be unblocked to start using C++11 liberally.  While there was consensus I do not remember an announcement - that's a key problem with boost today - the lack of a decision body that can steer the ship in (hopefully) the right direction.

---

## Review 6 [Approved]

> Username: jeking3  
> Created_at: 2018-09-06 11:21:55 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/date_time/pull/86#pullrequestreview-152883965  

I suspect that once boost in general drops any official C++03 support, everyone is going to go directly to nullptr.  In the meantime, this looks fine to me.

---

## Comment 7

> Username: JeffGarland  
> Created_at: 2018-09-06 22:32:53 UTC  
> Url: https://github.com/boostorg/date_time/pull/86#issuecomment-419262670  

@jeking3 sorry I missed the mailing list discussion.  There was also a long extended discussion this year in Aspen.  Also in polls, majority of users had already moved to 11. There wasn't a final decision, but it's along the lines of what you're stating.    
  
Personally I'm ready to move forward.  Fine to approve this for now -- no reason not to get rid of ugly warnings, but I do expect the code base to be refactored to newer C++ -- very soon.

---
