# #41 Fixed integer overflow when adding many years to a gregorian date. [Merged]

> Username: Lastique  
> Created at: 2017-08-08 11:20:18 UTC  
> Updated at: 2017-08-28 03:36:44 UTC  
> Merged at: 2017-08-08 14:30:04 UTC  
> Closed at: 2017-08-08 14:30:04 UTC  
> Url: https://github.com/boostorg/date_time/pull/41  

Also added a testcase to detect the overflow.  
  
Originated from this question: https://stackoverflow.com/questions/45537280/boost-datetime-issue-with-adding-long-year-durations

---

## Comment 1

> Username: mclow  
> Created_at: 2017-08-08 14:12:51 UTC  
> Url: https://github.com/boostorg/date_time/pull/41#issuecomment-320968428  

This would be a lot easier to review w/o all the extraneous whitespace changes.  (No, I'm not asking you to re-do the PR; I'm just saying)

---

## Review 2 [Commented]

> Username: mclow  
> Created_at: 2017-08-08 14:14:17 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/date_time/pull/41#pullrequestreview-54950302  

📁 include/boost/date_time/gregorian/greg_year.hpp

```diff
  34 |   /*! This small class allows for simple conversion an integer value into
  35 |       a year for the gregorian calendar.  This currently only allows a
  36 |       range of 1900 to 10000.  Both ends of the range are a bit arbitrary
```

> Username: mclow  
> Created_at: 2017-08-08 14:14:17 UTC  
> Updated_at: 2017-08-08 14:23:57 UTC  
> Url: https://github.com/boostorg/date_time/pull/41#discussion_r131923822  

Which is it? 1400 or 1900?   You've changed line 33 to say 1400, but line 36 says 1900.

> Username: Lastique  
> Created_at: 2017-08-08 14:25:23 UTC  
> Url: https://github.com/boostorg/date_time/pull/41#discussion_r131927360  

It's 1400, I missed the other place in the comment. You can see the valid range in the `greg_year_policies` definition above. I've updated the commit.


---

## Review 3 [Commented]

> Username: mclow  
> Created_at: 2017-08-08 14:25:15 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/date_time/pull/41#pullrequestreview-54954033  

📁 include/boost/date_time/adjust_functors.hpp

```diff
  75 |       wrap_int2 wi(ymd.month);
  76 |       //calc the year wrap around, add() returns 0 or 1 if wrapped
  77 |-       int_type year = wi.add(static_cast<int_type>(f_));
```

> Username: mclow  
> Created_at: 2017-08-08 14:25:15 UTC  
> Url: https://github.com/boostorg/date_time/pull/41#discussion_r131927321  

This is the only substantive change, right?

> Username: Lastique  
> Created_at: 2017-08-08 14:25:54 UTC  
> Url: https://github.com/boostorg/date_time/pull/41#discussion_r131927512  

Yes, it is. Plus the test, of course.


---

## Comment 4

> Username: Lastique  
> Created_at: 2017-08-08 14:29:56 UTC  
> Url: https://github.com/boostorg/date_time/pull/41#issuecomment-320973603  

> This would be a lot easier to review w/o all the extraneous whitespace changes.  
  
Yes, sorry, it's my editor - it automatically cleans trailing whitespaces and other stuff. I decided to leave it this way because personally I don't like untidiness like this.

---

## Comment 5

> Username: mclow  
> Created_at: 2017-08-08 14:30:35 UTC  
> Url: https://github.com/boostorg/date_time/pull/41#issuecomment-320973788  

Please keep an eye on the test bots, and remind me after 1.65.0 ships to merge to master.

---

## Comment 6

> Username: Lastique  
> Created_at: 2017-08-08 14:31:11 UTC  
> Url: https://github.com/boostorg/date_time/pull/41#issuecomment-320974003  

Ok, thanks.

---

## Comment 7

> Username: JeffGarland  
> Created_at: 2017-08-08 16:01:34 UTC  
> Url: https://github.com/boostorg/date_time/pull/41#issuecomment-321001905  

Thanks - that looks like a good fix to a forever bug.  Will be interesting to see if that cast was there to make some compiler happy.  In any case, I'm wondering if we shouldn't make the test push the boundaries.  That is, starting with date min at 1400-1-1 add ~7599 years.  That would ensure there's no overflow in the full range date-time supports.

---

## Comment 8

> Username: Lastique  
> Created_at: 2017-08-08 16:34:58 UTC  
> Url: https://github.com/boostorg/date_time/pull/41#issuecomment-321011008  

No problem, I expanded the tested range of values in https://github.com/boostorg/date_time/pull/43. The current code handles it without errors.

---

## Comment 9

> Username: Lastique  
> Created_at: 2017-08-22 19:42:31 UTC  
> Url: https://github.com/boostorg/date_time/pull/41#issuecomment-324130984  

There are no new test failures, and the added tests pass. Could you merge this PR, https://github.com/boostorg/date_time/pull/42, https://github.com/boostorg/date_time/pull/43 and https://github.com/boostorg/date_time/pull/44 to master, please?

---

## Comment 10

> Username: mclow  
> Created_at: 2017-08-28 03:36:44 UTC  
> Url: https://github.com/boostorg/date_time/pull/41#issuecomment-325252590  

Done. Thanks!

---
