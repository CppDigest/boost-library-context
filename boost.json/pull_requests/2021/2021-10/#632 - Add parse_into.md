# #632 Add parse_into [Closed]

> Username: vinniefalco  
> Created at: 2021-10-04 19:03:47 UTC  
> Updated at: 2021-10-05 15:11:34 UTC  
> Closed at: 2021-10-05 15:11:34 UTC  
> Url: https://github.com/boostorg/json/pull/632  

@pdimov no idea how to fix this compile error. I renamed the handler to `into_handler`.

---

## Comment 1

> Username: pdimov  
> Created_at: 2021-10-04 19:14:08 UTC  
> Url: https://github.com/boostorg/json/pull/632#issuecomment-933777565  

What compile error?

---

## Comment 2

> Username: vinniefalco  
> Created_at: 2021-10-04 19:22:30 UTC  
> Url: https://github.com/boostorg/json/pull/632#issuecomment-933783921  

https://gist.github.com/vinniefalco/ebc35f725bc3218b00044ab2b3ba2b9b

---

## Comment 3

> Username: pdimov  
> Created_at: 2021-10-04 19:29:10 UTC  
> Url: https://github.com/boostorg/json/pull/632#issuecomment-933788898  

If it's only under C++14 but disappears under C++17, I have an idea why that is. Put this thing on a branch `feature/parse-into`, where I can work on it. But probably fix the merge conflicts first.

---

## Comment 4

> Username: vinniefalco  
> Created_at: 2021-10-04 19:36:13 UTC  
> Url: https://github.com/boostorg/json/pull/632#issuecomment-933793622  

I resolved the conflicts: https://github.com/boostorg/json/tree/feature-parse-into

---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2021-10-04 20:48:38 UTC  
> Url: https://github.com/boostorg/json/pull/632#issuecomment-933843589  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/632/pullrequest-condensed-35db359.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/632/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/632/pullrequest.html)

---

## Comment 6

> Username: cppalliance-bot  
> Created_at: 2021-10-05 01:03:40 UTC  
> Url: https://github.com/boostorg/json/pull/632#issuecomment-933974156  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/632/pullrequest-condensed-29ebaba.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/632/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/632/pullrequest.html)

---

## Comment 7

> Username: cppalliance-bot  
> Created_at: 2021-10-05 02:53:39 UTC  
> Url: https://github.com/boostorg/json/pull/632#issuecomment-934015102  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/632/pullrequest-condensed-6cf5ef3.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/632/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/632/pullrequest.html)

---

## Comment 8

> Username: cppalliance-bot  
> Created_at: 2021-10-05 05:48:39 UTC  
> Url: https://github.com/boostorg/json/pull/632#issuecomment-934087185  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/632/pullrequest-condensed-9d52da3.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/632/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/632/pullrequest.html)

---
