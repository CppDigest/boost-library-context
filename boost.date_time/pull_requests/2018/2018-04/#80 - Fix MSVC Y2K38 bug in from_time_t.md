# #80 Fix MSVC Y2K38 bug in from_time_t [Merged]

> Username: orudge  
> Created at: 2018-04-19 13:39:56 UTC  
> Updated at: 2022-03-01 16:15:23 UTC  
> Merged at: 2022-03-01 16:15:23 UTC  
> Closed at: 2022-03-01 16:15:23 UTC  
> Url: https://github.com/boostorg/date_time/pull/80  

from_time_t was altered recently to cast the incoming value to a long. On LLP64 platforms, such as Windows, this truncates the value to 32-bit. My proposed patch casts to int64_t.  
  
I've also added a unit test involving a 64-bit time_t conversion.  
  
This fixes #87

---

## Comment 1

> Username: mclow  
> Created_at: 2018-04-19 14:52:20 UTC  
> Url: https://github.com/boostorg/date_time/pull/80#issuecomment-382766147  

I'll take a look. Note that your tests are failing on some platforms.  I *suspect* that casting to long was the wrong approach as well - but I've got to do some investigation first.  
  
Also - your test addition probably needs to be inside an `#ifdef` block, rather than `if (...)` because it might throw warnings (or even errors) on systems w/o 64 bit ints.

---

## Review 2 [Changes requested]

> Username: jeking3  
> Created_at: 2018-04-20 00:01:21 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/date_time/pull/80#pullrequestreview-113815743  

Please fix so all unit tests pass (including the new one).

---

## Comment 3

> Username: orudge  
> Created_at: 2018-04-20 08:58:37 UTC  
> Url: https://github.com/boostorg/date_time/pull/80#issuecomment-383032767  

Thanks, I'll check what's going on with the unit tests and will look at putting it into an #ifdef too.

---

## Comment 4

> Username: orudge  
> Created_at: 2018-04-20 13:35:36 UTC  
> Url: https://github.com/boostorg/date_time/pull/80#issuecomment-383097825  

I've updated the code to fix the #ifdef issue and ensure the time_t variable is definitely being populated with a 64-bit value, but it seems this is still failing - the internal seconds value in the ptime still seems to be truncated to 32-bit. I don't really understand why to be honest; I'm able to step through using MSVC 2017 (x86 build) and it's all working OK for me. This fix was also sufficient to sort out our application.  
  
It may be there's something deeper down in the headers causing issues, but unfortunately I'm a bit out of my depth trying to solve that. If my code can be of use then please free free to adapt it. At the very least the time_t to long truncation definitely appears to be wrong, but whether my fix is the 'right' one I'm not sure.

---

## Comment 5

> Username: jeking3  
> Created_at: 2018-11-12 13:26:30 UTC  
> Url: https://github.com/boostorg/date_time/pull/80#issuecomment-437879152  

Related to #87.

---

## Comment 6

> Username: jeking3  
> Created_at: 2018-12-02 00:37:22 UTC  
> Url: https://github.com/boostorg/date_time/pull/80#issuecomment-443470703  

Internally the code converts to nanoseconds, and 64 bits is insufficient to hold the conversion.  The issue is down in `boost/date_time/time_resolution_traits.hpp`, specifically in to_tick_count.  The unit test case added causes an int64_t overflow.

---

## Comment 7

> Username: mclow  
> Created_at: 2018-12-02 01:16:52 UTC  
> Url: https://github.com/boostorg/date_time/pull/80#issuecomment-443472312  

2^64 nanoseconds is ~584 years. Are we really testing for +/- 600 years?

---

## Comment 8

> Username: jeking3  
> Created_at: 2018-12-02 02:59:30 UTC  
> Url: https://github.com/boostorg/date_time/pull/80#issuecomment-443476846  

Yes.  I added a test for the max date to time_t conversation and it fails.  
Looking into what I can do without getting too invasive.  
  
On Sat, Dec 1, 2018, 8:16 PM Marshall Clow <notifications@github.com wrote:  
  
> 2^64 nanoseconds is ~584 years. Are we really testing for +/- 600 years?  
>  
> —  
> You are receiving this because you commented.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/date_time/pull/80#issuecomment-443472312>,  
> or mute the thread  
> <https://github.com/notifications/unsubscribe-auth/ALOdbYPTzn6My9JW2qHVrwrSRgHxRbnSks5u0yoFgaJpZM4Tb0I2>  
> .  
>

---

## Comment 9

> Username: jeking3  
> Created_at: 2018-12-02 15:17:14 UTC  
> Url: https://github.com/boostorg/date_time/pull/80#issuecomment-443515268  

@mclow current unit tests only test conversion to and from time_t up to year 2032 because the library only supported 32-bit time_t (when it was created).  I added a test locally to do time_t conversion to/from the maximum supported date (year 9999) and it fails because internally ticks are nanoseconds, and there isn't enough space for the conversions.  I'm experimenting with putting in a 128 bit internal ticks right now, but we'll see.. that may not be the best answer.

---

## Comment 10

> Username: jeking3  
> Created_at: 2022-02-12 02:16:52 UTC  
> Url: https://github.com/boostorg/date_time/pull/80#issuecomment-1036948725  

@orudge rebase on develop for a complete CI scan

---

## Comment 11

> Username: codecov[bot]  
> Created_at: 2022-03-01 00:09:25 UTC  
> Updated_at: 2022-03-01 16:01:38 UTC  
> Url: https://github.com/boostorg/date_time/pull/80#issuecomment-1054836347  

# [Codecov](https://codecov.io/gh/boostorg/date_time/pull/80?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#80](https://codecov.io/gh/boostorg/date_time/pull/80?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (54f0225) into [develop](https://codecov.io/gh/boostorg/date_time/commit/ff7716226569868de1d36036ceac01f5e1f0c669?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (ff77162) will **not change** coverage.  
> The diff coverage is `100.00%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/date_time/pull/80/graphs/tree.svg?width=650&height=150&src=pr&token=nDoh7t8f6g&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/date_time/pull/80?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop      #80   +/-   ##  
========================================  
  Coverage    91.70%   91.70%             
========================================  
  Files           77       77             
  Lines         4823     4823             
========================================  
  Hits          4423     4423             
  Misses         400      400             
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/date_time/pull/80?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/date\_time/posix\_time/conversion.hpp](https://codecov.io/gh/boostorg/date_time/pull/80/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9kYXRlX3RpbWUvcG9zaXhfdGltZS9jb252ZXJzaW9uLmhwcA==) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/date_time/pull/80?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/date_time/pull/80?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [ff77162...54f0225](https://codecov.io/gh/boostorg/date_time/pull/80?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).

---

## Review 12 [Commented]

> Username: jeking3  
> Created_at: 2022-03-01 13:16:00 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/date_time/pull/80#pullrequestreview-896284962  

📁 include/boost/date_time/posix_time/conversion.hpp

```diff
  28 |   {
  29 |-     return ptime(gregorian::date(1970,1,1)) + seconds(static_cast<long>(t));
  29 |+     return ptime(gregorian::date(1970,1,1)) + seconds(static_cast<boost::int64_t>(t));
```

> Username: jeking3  
> Created_at: 2022-03-01 13:15:52 UTC  
> Updated_at: 2022-03-01 13:16:00 UTC  
> Url: https://github.com/boostorg/date_time/pull/80#discussion_r816757571  

See alternative suggestion in https://github.com/boostorg/date_time/issues/87 - is the cast necessary here?


---

## Review 13 [Approved]

> Username: jeking3  
> Created_at: 2022-03-01 13:37:10 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/date_time/pull/80#pullrequestreview-896307983  

If you look back through the history of this line, it was originally without a seconds cast.  Then it was cast to long to avoid a compiler error, which likely happened right before a change was made in 1.67.0 that made the cast unnecessary, so we're back to not having a cast again.  
 https://github.com/boostorg/date_time/blob/acbee0dc6a168289c398041e91e053b3684083fc/include/boost/date_time/time_resolution_traits.hpp#L86-L94

---

## Comment 14

> Username: jeking3  
> Created_at: 2022-03-01 14:28:50 UTC  
> Url: https://github.com/boostorg/date_time/pull/80#issuecomment-1055502700  

It's likely worth combing through some of the builds to see the state of warnings around that change before merging.

---
