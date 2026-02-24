# #99 Improve performance of adding and subtracting time durations from ptime [Merged]

> Username: gawain-bolton  
> Created at: 2019-03-09 19:57:47 UTC  
> Updated at: 2019-10-10 20:07:49 UTC  
> Merged at: 2019-10-10 20:07:49 UTC  
> Closed at: 2019-10-10 20:07:49 UTC  
> Url: https://github.com/boostorg/date_time/pull/99  

Modifying ptime objects by adding and subtracting time durations was  
inefficient because it extracted the date and time of day and then  
re-constructed a ptime using the date and modified time of day.  
  
This can be avoided by using the existing time_system utilities which  
perform the operation by adjusting the number of ticks.  
  
Performance is improved by a factor of 48 on my system.

---

## Comment 1

> Username: mclow  
> Created_at: 2019-03-09 20:23:08 UTC  
> Url: https://github.com/boostorg/date_time/pull/99#issuecomment-471218841  

> Performance is improved by a factor of 48 on my system.  
  
Performance of *what*?

---

## Comment 2

> Username: gawain-bolton  
> Created_at: 2019-03-09 20:45:42 UTC  
> Url: https://github.com/boostorg/date_time/pull/99#issuecomment-471220425  

> > Performance is improved by a factor of 48 on my system.  
>   
> Performance of _what_?  
  
The performance of modifying a ptime object by adding/subtracting a time duration.

---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2019-03-09 22:21:25 UTC  
> Updated_at: 2019-10-08 20:38:24 UTC  
> Url: https://github.com/boostorg/date_time/pull/99#issuecomment-471227570  

# [Codecov](https://codecov.io/gh/boostorg/date_time/pull/99?src=pr&el=h1) Report  
> Merging [#99](https://codecov.io/gh/boostorg/date_time/pull/99?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/date_time/commit/3cc68a4642d9f5f319b4b994f16c6e045cd44499?src=pr&el=desc) will **increase** coverage by `11.13%`.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/date_time/pull/99/graphs/tree.svg?width=650&token=nDoh7t8f6g&height=150&src=pr)](https://codecov.io/gh/boostorg/date_time/pull/99?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #99       +/-   ##  
===========================================  
+ Coverage    54.17%   65.3%   +11.13%       
===========================================  
  Files           83       3       -80       
  Lines         4984      49     -4935       
  Branches      2368      17     -2351       
===========================================  
- Hits          2700      32     -2668       
+ Misses         398       5      -393       
+ Partials      1886      12     -1874  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/date_time/pull/99?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/date\_time/gregorian/greg\_day.hpp](https://codecov.io/gh/boostorg/date_time/pull/99/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9kYXRlX3RpbWUvZ3JlZ29yaWFuL2dyZWdfZGF5LmhwcA==) | | |  
| [...t/date\_time/posix\_time/date\_duration\_operators.hpp](https://codecov.io/gh/boostorg/date_time/pull/99/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9kYXRlX3RpbWUvcG9zaXhfdGltZS9kYXRlX2R1cmF0aW9uX29wZXJhdG9ycy5ocHA=) | | |  
| [include/boost/date\_time/adjust\_functors.hpp](https://codecov.io/gh/boostorg/date_time/pull/99/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9kYXRlX3RpbWUvYWRqdXN0X2Z1bmN0b3JzLmhwcA==) | | |  
| [include/boost/date\_time/gregorian/greg\_date.hpp](https://codecov.io/gh/boostorg/date_time/pull/99/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9kYXRlX3RpbWUvZ3JlZ29yaWFuL2dyZWdfZGF0ZS5ocHA=) | | |  
| [...clude/boost/date\_time/special\_values\_formatter.hpp](https://codecov.io/gh/boostorg/date_time/pull/99/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9kYXRlX3RpbWUvc3BlY2lhbF92YWx1ZXNfZm9ybWF0dGVyLmhwcA==) | | |  
| [...lude/boost/date\_time/posix\_time/time\_serialize.hpp](https://codecov.io/gh/boostorg/date_time/pull/99/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9kYXRlX3RpbWUvcG9zaXhfdGltZS90aW1lX3NlcmlhbGl6ZS5ocHA=) | | |  
| [include/boost/date\_time/constrained\_value.hpp](https://codecov.io/gh/boostorg/date_time/pull/99/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9kYXRlX3RpbWUvY29uc3RyYWluZWRfdmFsdWUuaHBw) | | |  
| [include/boost/date\_time/format\_date\_parser.hpp](https://codecov.io/gh/boostorg/date_time/pull/99/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9kYXRlX3RpbWUvZm9ybWF0X2RhdGVfcGFyc2VyLmhwcA==) | | |  
| [include/boost/date\_time/period\_parser.hpp](https://codecov.io/gh/boostorg/date_time/pull/99/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9kYXRlX3RpbWUvcGVyaW9kX3BhcnNlci5ocHA=) | | |  
| [...clude/boost/date\_time/local\_time/local\_time\_io.hpp](https://codecov.io/gh/boostorg/date_time/pull/99/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9kYXRlX3RpbWUvbG9jYWxfdGltZS9sb2NhbF90aW1lX2lvLmhwcA==) | | |  
| ... and [69 more](https://codecov.io/gh/boostorg/date_time/pull/99/diff?src=pr&el=tree-more) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/date_time/pull/99?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/date_time/pull/99?src=pr&el=footer). Last update [3cc68a4...941f0f4](https://codecov.io/gh/boostorg/date_time/pull/99?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Review 4 [Approved]

> Username: jeking3  
> Created_at: 2019-03-10 12:17:19 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/date_time/pull/99#pullrequestreview-212599845  

The codecov failure indicates that there are no other unit testing code paths that exercise the code that was replaced.  Coverage in this module overall is pretty spotty though, and it didn't drop a lot.

---

## Review 5 [Commented]

> Username: JeffGarland  
> Created_at: 2019-03-11 14:46:00 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/date_time/pull/99#pullrequestreview-212878712  

📁 include/boost/date_time/time.hpp

```diff
 166 |     {
 167 |-       time_ = (time_system::get_time_rep(date(), time_of_day() + td));
 167 |+       time_ = time_system::add_time_duration(time_,td);
```

> Username: JeffGarland  
> Created_at: 2019-03-11 14:46:00 UTC  
> Updated_at: 2019-10-08 20:38:22 UTC  
> Url: https://github.com/boostorg/date_time/pull/99#discussion_r264266497  

I realize we've already pulled this, but I'm just wondering if we really have coverage for split time systems that use things other than single integers here. See this page for info on the 96 bit representations.  My guess is that the options is a) rarely used, b) not unit tested, and c) probably ok -- but we should double check...  
  
https://www.boost.org/doc/libs/1_69_0/doc/html/date_time/details.html#date_time.buildinfo

> Username: jeking3  
> Created_at: 2019-03-13 14:36:40 UTC  
> Updated_at: 2019-10-08 20:38:22 UTC  
> Url: https://github.com/boostorg/date_time/pull/99#discussion_r265158626  

This change wasn't merged yet.


---

## Comment 6

> Username: gawain-bolton  
> Created_at: 2019-03-29 07:02:28 UTC  
> Url: https://github.com/boostorg/date_time/pull/99#issuecomment-477891563  

Please let me know if there is anything I need to change for this pull request to be accepted.

---

## Comment 7

> Username: cclienti  
> Created_at: 2019-09-25 09:09:00 UTC  
> Updated_at: 2019-10-06 09:22:08 UTC  
> Url: https://github.com/boostorg/date_time/pull/99#issuecomment-534928587  

Hello,  
  
This pull-request would be a great improvements for me. I checked the fail tests and it seems that it is a bit fragile if the minutes / seconds change at certain moment during the test.  
  
```cpp  
    ptime t1 = second_clock::local_time();  
    std::cout << to_simple_string(t1) << std::endl;  
  
    ptime t2 = microsec_clock::local_time();  
    std::cout << to_simple_string(t2) << std::endl;  
    check("hours match", t1.time_of_day().hours() == t2.time_of_day().hours());  
    check("minutes match",   
          t1.time_of_day().minutes() == t2.time_of_day().minutes());  
    check("seconds match",   
          t1.time_of_day().minutes() == t2.time_of_day().minutes());  
    check("hours date", t1.date() == t2.date());  
    if( !check("last is less", last <= t2) ) {  
      std::cout << to_simple_string(last) << " < "   
        << to_simple_string(t2) << std::endl;  
    }  
```  
if the minute changes during t1 affectation and t2 affectation, the check will fail.  
  
See https://github.com/boostorg/date_time/pull/109  
  
Christophe

---

## Comment 8

> Username: gawain-bolton  
> Created_at: 2019-10-10 18:56:31 UTC  
> Url: https://github.com/boostorg/date_time/pull/99#issuecomment-540727083  

Once testmicrosec_time_clock failed which has nothing to do with this patch.  
Is there no desire to have any improvements made whatsoever to this project?

---
