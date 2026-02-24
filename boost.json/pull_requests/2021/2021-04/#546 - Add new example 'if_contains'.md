# #546 Add new example 'if_contains' [Closed]

> Username: mxp1983  
> Created at: 2021-04-24 17:53:08 UTC  
> Updated at: 2021-04-26 15:07:29 UTC  
> Closed at: 2021-04-26 15:07:29 UTC  
> Url: https://github.com/boostorg/json/pull/546  

The motivation is to have a method checking if the nested object exists or not, for example:  
  
`if_contains(&jv, "nested", "array", 1, "array.2.o");`  
  
It will return a const pointer to the value if it exists at a given path or nullptr otherwise.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2021-04-24 18:11:05 UTC  
> Updated_at: 2021-04-24 18:58:04 UTC  
> Url: https://github.com/boostorg/json/pull/546#issuecomment-826132081  

# [Codecov](https://codecov.io/gh/boostorg/json/pull/546?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#546](https://codecov.io/gh/boostorg/json/pull/546?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (b7eab61) into [develop](https://codecov.io/gh/boostorg/json/commit/9403530b598b271be004597a1d6bb7755ecf09dd?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (9403530) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/json/pull/546/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/json/pull/546?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #546   +/-   ##  
========================================  
  Coverage    99.10%   99.10%             
========================================  
  Files           67       67             
  Lines         6049     6049             
========================================  
  Hits          5995     5995             
  Misses          54       54             
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/json/pull/546?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/json/pull/546?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [9403530...b7eab61](https://codecov.io/gh/boostorg/json/pull/546?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).

---

## Comment 2

> Username: vinniefalco  
> Created_at: 2021-04-24 18:49:41 UTC  
> Url: https://github.com/boostorg/json/pull/546#issuecomment-826137266  

Does the JSON Pointer specification also solve this problem?

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2021-04-24 19:09:04 UTC  
> Url: https://github.com/boostorg/json/pull/546#issuecomment-826139788  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/546/pullrequest-condensed-948f92a.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/546/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/546/pullrequest.html)

---

## Comment 4

> Username: mxp1983  
> Created_at: 2021-04-24 19:12:59 UTC  
> Url: https://github.com/boostorg/json/pull/546#issuecomment-826140316  

> Does the JSON Pointer specification also solve this problem?  
  
Yes, usage like:  
`if_contains(&jv, "/nested/array/1/array.2.o");`  
would be also fine.  
  
Would you like me to provide the exemplary implementation of it or is it already available?

---

## Comment 5

> Username: mxp1983  
> Created_at: 2021-04-25 19:45:45 UTC  
> Url: https://github.com/boostorg/json/pull/546#issuecomment-826379372  

I have checked JSON Pointer specification in more detail and its implementation deserves a separate PR.  
  
Is this PR good to be merged, or is it redundant/superfluous or is there something still missing?

---

## Comment 6

> Username: vinniefalco  
> Created_at: 2021-04-25 19:49:11 UTC  
> Url: https://github.com/boostorg/json/pull/546#issuecomment-826379770  

Nothing wrong with an example I suppose, @grisumbras ?

---

## Comment 7

> Username: grisumbras  
> Created_at: 2021-04-25 20:30:49 UTC  
> Url: https://github.com/boostorg/json/pull/546#issuecomment-826385446  

I kinda feel that with JSON Pointer (which I plan to have full support for by the end of May) this example becomes completely unnecessary.

---

## Comment 8

> Username: mxp1983  
> Created_at: 2021-04-26 06:46:03 UTC  
> Url: https://github.com/boostorg/json/pull/546#issuecomment-826553643  

@grisumbras: you are correct. If JSON Pointer is coming in Boost::Json 1.77, then this example is redundant. Do you need some help with your implementation? Maybe I could write some tests?  
  
@vinniefalco: should I just close this PR, or do you have an option to delete this PR?

---

## Comment 9

> Username: vinniefalco  
> Created_at: 2021-04-26 14:07:14 UTC  
> Url: https://github.com/boostorg/json/pull/546#issuecomment-826864485  

> do you have an option to delete this PR?  
  
There's no way to delete a pull request or issue that I know of

---

## Comment 10

> Username: mxp1983  
> Created_at: 2021-04-26 15:07:29 UTC  
> Url: https://github.com/boostorg/json/pull/546#issuecomment-826911597  

In this case, I close the PR.  
  
@grisumbras, please write me on Slack in case you would like to have some help with the implementation or testing.

---
