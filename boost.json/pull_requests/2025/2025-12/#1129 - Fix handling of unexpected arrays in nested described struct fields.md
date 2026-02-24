# #1129 Fix handling of unexpected arrays in nested described struct fields [Merged]

> Username: freitass  
> Created at: 2025-12-04 20:10:53 UTC  
> Updated at: 2025-12-08 16:47:29 UTC  
> Merged at: 2025-12-08 10:00:50 UTC  
> Closed at: 2025-12-08 10:00:50 UTC  
> Url: https://github.com/boostorg/json/pull/1129  

Fixes a corner case where, when parsing unexpected arrays in nested JSON objects, the `ignoring_handler` would incorrectly signal `signal_end` instead of `signal_value`, causing the parser to skip the rest of the containing object.  
  
```cpp  
struct X  
{  
    int a;  
    float b;  
    std::string c;  
};  
  
BOOST_DESCRIBE_STRUCT(X, (), (a, b, c))  
  
struct W  
{  
    X x;  
};  
  
BOOST_DESCRIBE_STRUCT(W, (), (x))  
  
// ...  
  
const auto json = R"(  
{  
    "x": {  
        "unexpected": [],  
        "a": 1,  
        "b": 3.14,  
        "c": "hello"  
    }  
})";  
  
W w{};  
std::error_code ec;  
parse_into(w, json, ec);  
  
BOOST_TEST( !ec );               // Ok.  
BOOST_TEST( w.x.a == 1 );        // Error: w.x.a is 0  
BOOST_TEST( w.x.b == 3.14f );    // Error: w.x.b is 0.0  
BOOST_TEST( w.x.c == "hello" );  // Error: w.x.c is ""  
```

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2025-12-04 20:17:39 UTC  
> Updated_at: 2025-12-05 14:22:01 UTC  
> Url: https://github.com/boostorg/json/pull/1129#issuecomment-3614167510  

An automated preview of the documentation is available at [https://1129.json.prtest2.cppalliance.org/libs/json/doc/html/index.html](https://1129.json.prtest2.cppalliance.org/libs/json/doc/html/index.html)  
  
If more commits are pushed to the pull request, the docs will rebuild at the same URL.  
  
2025-12-05 14:22:00 UTC

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2025-12-04 21:19:19 UTC  
> Url: https://github.com/boostorg/json/pull/1129#issuecomment-3614367803  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1129/pullrequest-condensed-384f70d.png)  
 Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1129/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1129/pullrequest.html)

---

## Comment 3

> Username: grisumbras  
> Created_at: 2025-12-05 11:43:08 UTC  
> Url: https://github.com/boostorg/json/pull/1129#issuecomment-3616550890  

First, thank you for catching and fixing this.  
  
Please, rebase on develop so that CI jobs succeed.

---

## Comment 4

> Username: codecov[bot]  
> Created_at: 2025-12-05 15:02:54 UTC  
> Updated_at: 2025-12-08 16:47:29 UTC  
> Url: https://github.com/boostorg/json/pull/1129#issuecomment-3617280030  

## [Codecov](https://app.codecov.io/gh/boostorg/json/pull/1129?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:white_check_mark: All modified and coverable lines are covered by tests.  
:white_check_mark: Project coverage is 93.67%. Comparing base ([`91b6516`](https://app.codecov.io/gh/boostorg/json/commit/91b65160e2b15159c439a6e97f574ab019592b6e?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`707a450`](https://app.codecov.io/gh/boostorg/json/commit/707a450ade0ecbfff83fa47d5be2e146dcc24e06?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
:warning: Report is 1 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/json/pull/1129/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/json/pull/1129?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1129      +/-   ##  
===========================================  
- Coverage    93.71%   93.67%   -0.05%       
===========================================  
  Files           91       91                
  Lines         9153     9153                
===========================================  
- Hits          8578     8574       -4       
- Misses         575      579       +4       
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/json/pull/1129?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/detail/parse\_into.hpp](https://app.codecov.io/gh/boostorg/json/pull/1129?src=pr&el=tree&filepath=include%2Fboost%2Fjson%2Fdetail%2Fparse_into.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9wYXJzZV9pbnRvLmhwcA==) | `99.33% <100.00%> (-0.67%)` | :arrow_down: |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/json/pull/1129?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/json/pull/1129?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [91b6516...707a450](https://app.codecov.io/gh/boostorg/json/pull/1129?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2025-12-05 15:23:51 UTC  
> Url: https://github.com/boostorg/json/pull/1129#issuecomment-3617359046  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1129/pullrequest-condensed-5f19ffd.png)  
 Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1129/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1129/pullrequest.html)

---

## Comment 6

> Username: freitass  
> Created_at: 2025-12-05 15:33:57 UTC  
> Url: https://github.com/boostorg/json/pull/1129#issuecomment-3617401906  

Branch updated, not sure why the coverage dropped -0.05%.

---

## Comment 7

> Username: grisumbras  
> Created_at: 2025-12-05 15:36:16 UTC  
> Url: https://github.com/boostorg/json/pull/1129#issuecomment-3617409637  

Seems like it's a coverage service bug. I'm discussing dropping that. Bad coverage reports are worse than no coverage reports.

---
