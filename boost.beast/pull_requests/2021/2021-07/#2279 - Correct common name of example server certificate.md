# #2279 Correct common name of example server certificate [Merged]

> Username: utsavm9  
> Created at: 2021-07-06 02:15:22 UTC  
> Updated at: 2022-06-21 23:39:25 UTC  
> Merged at: 2022-06-21 23:39:25 UTC  
> Closed at: 2022-06-21 23:39:25 UTC  
> Url: https://github.com/boostorg/beast/pull/2279  

Resolves https://github.com/boostorg/beast/issues/2266.  
  
Now, testing the async-ssl server with `curl https://localhost:8000/ --cacert cert.pem` will not give a relatively cryptic error message like:  
```  
curl: (60) SSL: unable to obtain common name from peer certificate  
More details here: https://curl.haxx.se/docs/sslcerts.html  
```  
but would hint the developer to create their own certificate by showing a message like:  
```  
curl: (60) SSL: certificate subject name 'www.example.com' does not match target host name 'localhost'  
```  
  
## Testing  
Compiled and ran the async-ssl server example code in a seperate CMake project. Could not figure out how to build the project in the `beast` repo as is, so I have also not run any tests locally.

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2021-07-06 02:51:04 UTC  
> Url: https://github.com/boostorg/beast/pull/2279#issuecomment-874420886  

![pullrequest](https://2279.beast.tracing.cppalliance.org/pullrequest-2acad47c.png)  
Timeline tracing charts: [https://2279.beast.tracing.cppalliance.org/index.html](https://2279.beast.tracing.cppalliance.org/index.html)

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2021-07-06 02:59:52 UTC  
> Updated_at: 2021-07-06 03:22:57 UTC  
> Url: https://github.com/boostorg/beast/pull/2279#issuecomment-874423385  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/2279?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#2279](https://codecov.io/gh/boostorg/beast/pull/2279?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (618d880) into [develop](https://codecov.io/gh/boostorg/beast/commit/710cc53331f197f6f17e8c38454c09df68e43c03?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (710cc53) will **decrease** coverage by `0.00%`.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/2279/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/beast/pull/2279?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #2279      +/-   ##  
===========================================  
- Coverage    95.13%   95.12%   -0.01%       
===========================================  
  Files          153      153                
  Lines        11977    11973       -4       
===========================================  
- Hits         11394    11389       -5       
- Misses         583      584       +1       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/2279?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/websocket/impl/ping.hpp](https://codecov.io/gh/boostorg/beast/pull/2279/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9waW5nLmhwcA==) | `96.42% <0.00%> (-0.90%)` | :arrow_down: |  
| [include/boost/beast/websocket/impl/error.ipp](https://codecov.io/gh/boostorg/beast/pull/2279/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9lcnJvci5pcHA=) | `88.23% <0.00%> (-0.23%)` | :arrow_down: |  
| [include/boost/beast/http/impl/error.ipp](https://codecov.io/gh/boostorg/beast/pull/2279/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ltcGwvZXJyb3IuaXBw) | `97.50% <0.00%> (-0.07%)` | :arrow_down: |  
| [include/boost/beast/core/impl/error.ipp](https://codecov.io/gh/boostorg/beast/pull/2279/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2ltcGwvZXJyb3IuaXBw) | `100.00% <0.00%> (ø)` | |  
| [include/boost/beast/zlib/impl/error.ipp](https://codecov.io/gh/boostorg/beast/pull/2279/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC96bGliL2ltcGwvZXJyb3IuaXBw) | `100.00% <0.00%> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/2279?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/2279?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [710cc53...618d880](https://codecov.io/gh/boostorg/beast/pull/2279?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).

---

## Comment 3

> Username: utsavm9  
> Created_at: 2021-07-06 03:33:44 UTC  
> Url: https://github.com/boostorg/beast/pull/2279#issuecomment-874433658  

I am guessing the coverage went down because some of the errors do not occur after the change, but I am not sure. Let me know what I can do to increase the coverage, if that is needed in this PR to merge.

---

## Comment 4

> Username: nancho84  
> Created_at: 2022-01-03 08:00:22 UTC  
> Url: https://github.com/boostorg/beast/pull/2279#issuecomment-1003921046  

[]()

---

## Comment 5

> Username: madmongo1  
> Created_at: 2022-01-03 08:25:50 UTC  
> Url: https://github.com/boostorg/beast/pull/2279#issuecomment-1003934125  

I'm happy to overlook the coverage "drop". I'll get it merged today

---

## Comment 6

> Username: vinniefalco  
> Created_at: 2022-06-21 23:37:37 UTC  
> Url: https://github.com/boostorg/beast/pull/2279#issuecomment-1162463673  

@alandefreitas please verify the commit is correct and then merge

---
