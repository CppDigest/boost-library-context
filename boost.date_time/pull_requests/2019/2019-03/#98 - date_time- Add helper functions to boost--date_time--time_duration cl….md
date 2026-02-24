# #98 date_time: Add helper functions to boost::date_time::time_duration cl… [Closed]

> Username: gawain-bolton  
> Created at: 2019-03-03 13:39:15 UTC  
> Updated at: 2019-10-31 20:04:35 UTC  
> Closed at: 2019-10-31 20:04:35 UTC  
> Url: https://github.com/boostorg/date_time/pull/98  

…ass.  
  
Currently the time_duration class has a function is_positive() to  
determine whether or not the duration is positive.  However, it is  
also often useful to know whether or not the duration is negative or  
zero.  The is_negative() and is_zero() functions are provided for this  
purpose.  
  
Finally, the abs() function returns the absolute value of the duration  
which can be useful when checking differences between durations.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2019-03-03 20:53:05 UTC  
> Updated_at: 2019-10-31 19:58:21 UTC  
> Url: https://github.com/boostorg/date_time/pull/98#issuecomment-469062980  

# [Codecov](https://codecov.io/gh/boostorg/date_time/pull/98?src=pr&el=h1) Report  
> Merging [#98](https://codecov.io/gh/boostorg/date_time/pull/98?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/date_time/commit/b0437e2999a65668dc4178dbb817a89a382ece94?src=pr&el=desc) will **decrease** coverage by `4.85%`.  
> The diff coverage is `50%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/date_time/pull/98/graphs/tree.svg?width=650&token=nDoh7t8f6g&height=150&src=pr)](https://codecov.io/gh/boostorg/date_time/pull/98?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop      #98      +/-   ##  
===========================================  
- Coverage    56.31%   51.46%   -4.86%       
===========================================  
  Files           83       83                
  Lines         4986     4992       +6       
  Branches      2443     2444       +1       
===========================================  
- Hits          2808     2569     -239       
- Misses         378      406      +28       
- Partials      1800     2017     +217  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/date_time/pull/98?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/date\_time/time\_duration.hpp](https://codecov.io/gh/boostorg/date_time/pull/98/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9kYXRlX3RpbWUvdGltZV9kdXJhdGlvbi5ocHA=) | `79.79% <50%> (-2.62%)` | :arrow_down: |  
| [include/boost/date\_time/time\_iterator.hpp](https://codecov.io/gh/boostorg/date_time/pull/98/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9kYXRlX3RpbWUvdGltZV9pdGVyYXRvci5ocHA=) | `63.63% <0%> (-27.28%)` | :arrow_down: |  
| [include/boost/date\_time/string\_convert.hpp](https://codecov.io/gh/boostorg/date_time/pull/98/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9kYXRlX3RpbWUvc3RyaW5nX2NvbnZlcnQuaHBw) | `75% <0%> (-25%)` | :arrow_down: |  
| [include/boost/date\_time/string\_parse\_tree.hpp](https://codecov.io/gh/boostorg/date_time/pull/98/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9kYXRlX3RpbWUvc3RyaW5nX3BhcnNlX3RyZWUuaHBw) | `55.73% <0%> (-22.96%)` | :arrow_down: |  
| [include/boost/date\_time/period\_formatter.hpp](https://codecov.io/gh/boostorg/date_time/pull/98/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9kYXRlX3RpbWUvcGVyaW9kX2Zvcm1hdHRlci5ocHA=) | `58.53% <0%> (-21.96%)` | :arrow_down: |  
| [include/boost/date\_time/strings\_from\_facet.hpp](https://codecov.io/gh/boostorg/date_time/pull/98/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9kYXRlX3RpbWUvc3RyaW5nc19mcm9tX2ZhY2V0LmhwcA==) | `56.25% <0%> (-18.75%)` | :arrow_down: |  
| [...oost/date\_time/posix\_time/posix\_time\_legacy\_io.hpp](https://codecov.io/gh/boostorg/date_time/pull/98/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9kYXRlX3RpbWUvcG9zaXhfdGltZS9wb3NpeF90aW1lX2xlZ2FjeV9pby5ocHA=) | `31.37% <0%> (-17.65%)` | :arrow_down: |  
| [...clude/boost/date\_time/local\_time/local\_time\_io.hpp](https://codecov.io/gh/boostorg/date_time/pull/98/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9kYXRlX3RpbWUvbG9jYWxfdGltZS9sb2NhbF90aW1lX2lvLmhwcA==) | `26.08% <0%> (-13.05%)` | :arrow_down: |  
| [include/boost/date\_time/tz\_db\_base.hpp](https://codecov.io/gh/boostorg/date_time/pull/98/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9kYXRlX3RpbWUvdHpfZGJfYmFzZS5ocHA=) | `42.57% <0%> (-12.88%)` | :arrow_down: |  
| [...de/boost/date\_time/local\_time/custom\_time\_zone.hpp](https://codecov.io/gh/boostorg/date_time/pull/98/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9kYXRlX3RpbWUvbG9jYWxfdGltZS9jdXN0b21fdGltZV96b25lLmhwcA==) | `35% <0%> (-11.67%)` | :arrow_down: |  
| ... and [27 more](https://codecov.io/gh/boostorg/date_time/pull/98/diff?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/date_time/pull/98?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/date_time/pull/98?src=pr&el=footer). Last update [b0437e2...941f0f4](https://codecov.io/gh/boostorg/date_time/pull/98?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Review 2 [Commented]

> Username: JeffGarland  
> Created_at: 2019-03-03 21:12:32 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/date_time/pull/98#pullrequestreview-209931057  

📁 include/boost/date_time/time_duration.hpp

```diff
  32 |-       @tparam T The subclass type
  33 |-       @tparam rep_type The time resolution traits for this duration type.
  32 |+       @param T The subclass type
```

> Username: JeffGarland  
> Created_at: 2019-03-03 21:12:32 UTC  
> Updated_at: 2019-10-31 19:58:13 UTC  
> Url: https://github.com/boostorg/date_time/pull/98#discussion_r261887352  

hmm I believe these where just change to tparam in another pull request...

> Username: jeking3  
> Created_at: 2019-03-05 18:06:56 UTC  
> Updated_at: 2019-10-31 19:58:13 UTC  
> Url: https://github.com/boostorg/date_time/pull/98#discussion_r262616817  

They were, so this should change to tparam.


---

## Review 3 [Commented]

> Username: JeffGarland  
> Created_at: 2019-03-03 21:22:46 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/date_time/pull/98#pullrequestreview-209931566  

📁 include/boost/date_time/time_duration.hpp

```diff
 145 |       return duration_type(ticks_ * (-1));
 146 |     }
 147 |+     duration_type abs() const
```

> Username: JeffGarland  
> Created_at: 2019-03-03 21:22:45 UTC  
> Updated_at: 2019-10-31 19:58:13 UTC  
> Url: https://github.com/boostorg/date_time/pull/98#discussion_r261887861  

These functions seem like a nice addition.  Unfortunately no one will no about them without documentation.  The docs are in boost book xml which is a bit clunky to modify, but it's pretty obvious.  You should be able to find the accessor table for time_duration add trivially add these.


---

## Review 4 [Commented]

> Username: JeffGarland  
> Created_at: 2019-03-03 21:24:27 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/date_time/pull/98#pullrequestreview-209931631  

📁 test/posix_time/testduration.cpp

```diff
  38 |+   check("invert_sign",td3 == td1.invert_sign());
  39 |+   check("abs",td3 == td1.abs());
  40 |+   check("is_positive",td3.is_positive());
```

> Username: JeffGarland  
> Created_at: 2019-03-03 21:24:27 UTC  
> Updated_at: 2019-10-31 19:58:13 UTC  
> Url: https://github.com/boostorg/date_time/pull/98#discussion_r261887939  

perhaps add an abs test case for a positive duration so that the coverage checking doesn't complain.


---

## Review 5 [Commented]

> Username: JeffGarland  
> Created_at: 2019-03-03 21:26:02 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/date_time/pull/98#pullrequestreview-209931721  

📁 test/posix_time/testduration.cpp

```diff
  38 |+   check("invert_sign",td3 == td1.invert_sign());
  39 |+   check("abs",td3 == td1.abs());
  40 |+   check("is_positive",td3.is_positive());
```

> Username: JeffGarland  
> Created_at: 2019-03-03 21:26:02 UTC  
> Updated_at: 2019-10-31 19:58:13 UTC  
> Url: https://github.com/boostorg/date_time/pull/98#discussion_r261888016  

And an is_positive for the false case.  You can see the unit test coverage is flagging missed paths.


---

## Review 6 [Changes requested]

> Username: jeking3  
> Created_at: 2019-03-04 16:28:58 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/date_time/pull/98#pullrequestreview-210238301  

Per @JeffGarland's comments, requesting changes.

---

## Comment 7

> Username: gawain-bolton  
> Created_at: 2019-03-04 21:15:30 UTC  
> Url: https://github.com/boostorg/date_time/pull/98#issuecomment-469424598  

Thank you @JeffGarland for your comments.  
I have added unit tests and documentation as you suggested.

---

## Review 8 [Approved]

> Username: JeffGarland  
> Created_at: 2019-03-04 21:56:36 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/date_time/pull/98#pullrequestreview-210388626  

Thank you for your contribution!

---

## Review 9 [Commented]

> Username: jeking3  
> Created_at: 2019-03-05 18:07:56 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/date_time/pull/98#pullrequestreview-210832130  

📁 include/boost/date_time/time_duration.hpp

```diff
 157 |       return ticks_ < 0;
 158 |     }
 159 |+     bool is_zero() const
```

> Username: jeking3  
> Created_at: 2019-03-05 18:07:56 UTC  
> Updated_at: 2019-10-31 19:58:13 UTC  
> Url: https://github.com/boostorg/date_time/pull/98#discussion_r262617172  

operator bool perhaps?  Just a thought.


---

## Review 10 [Changes requested]

> Username: jeking3  
> Created_at: 2019-03-05 18:08:52 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/date_time/pull/98#pullrequestreview-210832575  

Thanks for adding the documentation; the param vs. tparam comment is still a regression, and I was just curious if folks thought an explicit operator bool would be better than is_zero()... ?

---

## Comment 11

> Username: swatanabe  
> Created_at: 2019-03-05 18:23:57 UTC  
> Url: https://github.com/boostorg/date_time/pull/98#issuecomment-469794922  

AMDG  
  
On 3/5/19 11:08 AM, James E. King III wrote:  
> jeking3 requested changes on this pull request.  
>   
> Thanks for adding the documentation; the param vs. tparam comment is still a regression, and I was just curious if folks thought an explicit operator bool would be better than is_zero()... ?  
>   
  
I'm against explicit operator bool.  
  
In Christ,  
Steven Watanabe

---

## Comment 12

> Username: JeffGarland  
> Created_at: 2019-03-05 19:44:56 UTC  
> Url: https://github.com/boostorg/date_time/pull/98#issuecomment-469830802  

> AMDG  
> On 3/5/19 11:08 AM, James E. King III wrote: jeking3 requested changes on this pull request. Thanks for adding the documentation; the param vs. tparam comment is still a regression, and I was just curious if folks thought an explicit operator bool would be better than is_zero()... ?  
> I'm against explicit operator bool. In Christ, Steven Watanabe  
  
I agree with Steven on this -- this isn't a pointer, iterator, or an optional.  In fact if we were to add operator bool I would expect the behavior to be equivalent to checking for not_a_date_time.

---

## Comment 13

> Username: gawain-bolton  
> Created_at: 2019-03-05 20:32:23 UTC  
> Url: https://github.com/boostorg/date_time/pull/98#issuecomment-469846094  

@jeking3: The tparam vs param problem was fixed - I can see no differences between the boost develop branch and my forked develop branch.  
  
@ all:  
The travis-ci build failure is due to the fact that the testmicrosec_time_clock.cpp test can fail randomly.  
If you execute the test repeatedly, you will only have to wait a few minutes before you get 2 test failures.  
  
After looking at this unit test, it is not clear to me that is has much value.   It also seems to be incorrectly testing the equality of minutes twice instead of testing the equality of minutes and then seconds.

---

## Comment 14

> Username: jeking3  
> Created_at: 2019-03-06 00:09:52 UTC  
> Url: https://github.com/boostorg/date_time/pull/98#issuecomment-469911438  

Fair enough, just thought I would ask. :)

---

## Comment 15

> Username: JeffGarland  
> Created_at: 2019-03-06 11:38:18 UTC  
> Url: https://github.com/boostorg/date_time/pull/98#issuecomment-470075796  

> @ all:  
> The travis-ci build failure is due to the fact that the testmicrosec_time_clock.cpp test can fail randomly.  
  
Hmm what I found was:  
  
terminate called after throwing an instance of 'boost::archive::archive_exception'  
  
Which still would seem unrelated to the change.  
  
> If you execute the test repeatedly, you will only have to wait a few minutes before you get 2 test failures.  
>   
> After looking at this unit test, it is not clear to me that is has much value. It also seems to be incorrectly testing the equality of minutes twice instead of testing the equality of minutes and then seconds.  
  
That test has always been an issue, but it is kinda there to help evaluate how monotonic the clock is.  I'd have to look at it in detail again, but clearly that shouldn't hold up this change.

---

## Comment 16

> Username: jeking3  
> Created_at: 2019-03-09 15:40:57 UTC  
> Url: https://github.com/boostorg/date_time/pull/98#issuecomment-471191328  

REMINDER (TO MYSELF): SQUASH

---
