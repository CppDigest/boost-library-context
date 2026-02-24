# #2295 Add missing header from stream_state header [Closed]

> Username: laudrup  
> Created at: 2021-08-05 08:35:09 UTC  
> Updated at: 2021-08-05 11:39:56 UTC  
> Closed at: 2021-08-05 11:39:38 UTC  
> Url: https://github.com/boostorg/beast/pull/2295  

The stream_service class in stream_state makes use of  
boost::make_shared so ensure the header is included.  
  
This is required to include <beast/test/stream.hpp> from thirdparty  
code that hasn't included the make_shared header already.

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2021-08-05 09:10:35 UTC  
> Url: https://github.com/boostorg/beast/pull/2295#issuecomment-893297543  

![pullrequest](https://2295.beast.tracing.cppalliance.org/pullrequest-4fcade08.png)  
Timeline tracing charts: [https://2295.beast.tracing.cppalliance.org/index.html](https://2295.beast.tracing.cppalliance.org/index.html)

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2021-08-05 09:19:22 UTC  
> Updated_at: 2021-08-05 09:42:27 UTC  
> Url: https://github.com/boostorg/beast/pull/2295#issuecomment-893303597  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/2295?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#2295](https://codecov.io/gh/boostorg/beast/pull/2295?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (fdf4e46) into [develop](https://codecov.io/gh/boostorg/beast/commit/710cc53331f197f6f17e8c38454c09df68e43c03?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (710cc53) will **decrease** coverage by `0.04%`.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/2295/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/beast/pull/2295?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #2295      +/-   ##  
===========================================  
- Coverage    95.13%   95.08%   -0.05%       
===========================================  
  Files          153      153                
  Lines        11977    11973       -4       
===========================================  
- Hits         11394    11385       -9       
- Misses         583      588       +5       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/2295?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/websocket/impl/ping.hpp](https://codecov.io/gh/boostorg/beast/pull/2295/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9waW5nLmhwcA==) | `96.42% <0.00%> (-0.90%)` | :arrow_down: |  
| [include/boost/beast/websocket/impl/read.hpp](https://codecov.io/gh/boostorg/beast/pull/2295/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9yZWFkLmhwcA==) | `97.01% <0.00%> (-0.71%)` | :arrow_down: |  
| [include/boost/beast/websocket/impl/error.ipp](https://codecov.io/gh/boostorg/beast/pull/2295/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9lcnJvci5pcHA=) | `88.23% <0.00%> (-0.23%)` | :arrow_down: |  
| [include/boost/beast/http/impl/error.ipp](https://codecov.io/gh/boostorg/beast/pull/2295/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ltcGwvZXJyb3IuaXBw) | `97.50% <0.00%> (-0.07%)` | :arrow_down: |  
| [include/boost/beast/core/impl/error.ipp](https://codecov.io/gh/boostorg/beast/pull/2295/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2ltcGwvZXJyb3IuaXBw) | `100.00% <0.00%> (ø)` | |  
| [include/boost/beast/zlib/impl/error.ipp](https://codecov.io/gh/boostorg/beast/pull/2295/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC96bGliL2ltcGwvZXJyb3IuaXBw) | `100.00% <0.00%> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/2295?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/2295?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [710cc53...fdf4e46](https://codecov.io/gh/boostorg/beast/pull/2295?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).

---

## Comment 3

> Username: madmongo1  
> Created_at: 2021-08-05 11:06:56 UTC  
> Url: https://github.com/boostorg/beast/pull/2295#issuecomment-893369827  

Thanks for this. It's actually uncovered a few missing includes in that file. However, I don't think there is a dependency on make_shared in `include/boost/beast/_experimental/test/detail/stream_state.hpp`.  
  
Were you getting a compile error somewhere else?

---

## Comment 4

> Username: laudrup  
> Created_at: 2021-08-05 11:23:33 UTC  
> Updated_at: 2021-08-05 11:24:00 UTC  
> Url: https://github.com/boostorg/beast/pull/2295#issuecomment-893379791  

> Thanks for this. It's actually uncovered a few missing includes in that file. However, I don't think there is a dependency on make_shared in `include/boost/beast/_experimental/test/detail/stream_state.hpp`.  
>   
> Were you getting a compile error somewhere else?  
  
You are very welcome.  
  
It does seem like at least `boost/smart_ptr/shared_ptr.hpp` ought to be included as well and possibly more. I just wasn't sure.  
  
It's actually the implementation file that uses it (`.ipp`), but it looks like headers should be included in the corresponding header file and no the implementation file.  
  
I just found it slightly more useful to create a pull request than opening an issue. This at least fixes the issue for me :-)

---

## Comment 5

> Username: madmongo1  
> Created_at: 2021-08-05 11:35:39 UTC  
> Url: https://github.com/boostorg/beast/pull/2295#issuecomment-893387704  

https://github.com/madmongo1/beast/commit/8a2bffb9fc82fc6483f2789724c7ca4da09c64aa

---

## Comment 6

> Username: madmongo1  
> Created_at: 2021-08-05 11:39:56 UTC  
> Url: https://github.com/boostorg/beast/pull/2295#issuecomment-893390039  

https://github.com/madmongo1/beast/commit/5c9e8ad118460378d436191fa19fdff4371b5173

---
