# #2504 Remove unnecessary constant_time_size [Merged]

> Username: ecatmur  
> Created at: 2022-08-17 18:01:21 UTC  
> Updated at: 2022-10-02 11:57:28 UTC  
> Merged at: 2022-10-02 11:57:28 UTC  
> Closed at: 2022-10-02 11:57:28 UTC  
> Url: https://github.com/boostorg/beast/pull/2504  

http::basic_fields::set_t::size() is unused  
core::multi_buffer::list_type::size() is only compared to 1 (in shrink_to_fit()); compare iterators instead.

---

## Comment 1

> Username: vinniefalco  
> Created_at: 2022-08-17 18:02:41 UTC  
> Url: https://github.com/boostorg/beast/pull/2504#issuecomment-1218334843  

Are there any benchmarks?

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2022-08-17 18:27:20 UTC  
> Url: https://github.com/boostorg/beast/pull/2504#issuecomment-1218356155  

![pullrequest](https://2504.beast.tracing.cppalliance.org/pullrequest-3e6d18db.png)  
Timeline tracing charts: [https://2504.beast.tracing.cppalliance.org/index.html](https://2504.beast.tracing.cppalliance.org/index.html)

---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2022-08-17 18:56:10 UTC  
> Updated_at: 2022-08-17 18:56:48 UTC  
> Url: https://github.com/boostorg/beast/pull/2504#issuecomment-1218380451  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/2504?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#2504](https://codecov.io/gh/boostorg/beast/pull/2504?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (4319bff) into [develop](https://codecov.io/gh/boostorg/beast/commit/76043dec2cf67a2ba33b32bdcc129f5f0027b8be?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (76043de) will **decrease** coverage by `0.00%`.  
> The diff coverage is `100.00%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/2504/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/beast/pull/2504?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #2504      +/-   ##  
===========================================  
- Coverage    94.72%   94.71%   -0.01%       
===========================================  
  Files          152      152                
  Lines        11868    11868                
===========================================  
- Hits         11242    11241       -1       
- Misses         626      627       +1       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/2504?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/core/multi\_buffer.hpp](https://codecov.io/gh/boostorg/beast/pull/2504/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL211bHRpX2J1ZmZlci5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/beast/http/fields.hpp](https://codecov.io/gh/boostorg/beast/pull/2504/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ZpZWxkcy5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/beast/core/impl/multi\_buffer.hpp](https://codecov.io/gh/boostorg/beast/pull/2504/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2ltcGwvbXVsdGlfYnVmZmVyLmhwcA==) | `97.59% <100.00%> (ø)` | |  
| [include/boost/beast/websocket/impl/ping.hpp](https://codecov.io/gh/boostorg/beast/pull/2504/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9waW5nLmhwcA==) | `94.73% <0.00%> (-0.88%)` | :arrow_down: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/2504?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/2504?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [76043de...4319bff](https://codecov.io/gh/boostorg/beast/pull/2504?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).

---

## Comment 4

> Username: madmongo1  
> Created_at: 2022-08-17 20:45:15 UTC  
> Url: https://github.com/boostorg/beast/pull/2504#issuecomment-1218473419  

@ecatmur Thank you for taking the time to post the PR.  
What do we gain in practice from this change?  
I am thinking in terms of memory use, search times, insertion times and so on.

---

## Comment 5

> Username: ecatmur  
> Created_at: 2022-08-19 16:51:00 UTC  
> Url: https://github.com/boostorg/beast/pull/2504#issuecomment-1220886803  

> Are there any benchmarks?  
  
The only benchmark that is affected by this change is bench/buffers; I ran the benchmark (Ubuntu gcc 9, x86-64, release, threading-multi) but didn't observe any consistent differences in performance.  
  
> @ecatmur Thank you for taking the time to post the PR. What do we gain in practice from this change? I am thinking in terms of memory use, search times, insertion times and so on.  
  
http::fields reduces from 88 to 80 bytes (x86-64).  
multi_buffer reduces from 72 to 64 bytes.  
  
In terms of code size, multi_buffer::prepare() reduces from 1492 to 1378 bytes. There is no code change to fields test; gcc is smart enough to observe that the size field is never read, so entirely optimizes out stores to it.  
  
As such, this change is unlikely to improve insertion times, at least for release builds with decent compilers.  It's more of a cleanup exercise.

---

## Comment 6

> Username: vinniefalco  
> Created_at: 2022-08-19 19:06:10 UTC  
> Url: https://github.com/boostorg/beast/pull/2504#issuecomment-1221012034  

hey, bytes are bytes :) they start adding up

---

## Comment 7

> Username: madmongo1  
> Created_at: 2022-08-19 19:40:22 UTC  
> Url: https://github.com/boostorg/beast/pull/2504#issuecomment-1221035704  

I agree. I think we should accept this PR.

---

## Comment 8

> Username: klemens-morgenstern  
> Created_at: 2022-09-24 03:43:34 UTC  
> Url: https://github.com/boostorg/beast/pull/2504#issuecomment-1256847148  

@madmongo1 @vinniefalco What's preventing this from getting merged?

---

## Comment 9

> Username: vinniefalco  
> Created_at: 2022-09-25 00:40:25 UTC  
> Url: https://github.com/boostorg/beast/pull/2504#issuecomment-1257091462  

Merge it I guess. Note that in the new message model, the container is not node based anyway. It is just a string with the serialized message (plus a small table of offsets to allow random access).

---

## Comment 10

> Username: madmongo1  
> Created_at: 2022-09-25 08:23:17 UTC  
> Url: https://github.com/boostorg/beast/pull/2504#issuecomment-1257145887  

I though I had merged it

---
