# #472 fix drone asan build [Closed]

> Username: cppalliance-drone  
> Created at: 2020-11-24 22:47:51 UTC  
> Updated at: 2020-11-25 17:06:08 UTC  
> Closed at: 2020-11-25 17:06:08 UTC  
> Url: https://github.com/boostorg/json/pull/472  

A few of the jobs in drone were updated from Ubuntu 16 to Ubuntu 18.    Let's try switching asan back to Ubuntu 16.

---

## Comment 1

> Username: cppalliance-drone  
> Created_at: 2020-11-24 23:21:25 UTC  
> Url: https://github.com/boostorg/json/pull/472#issuecomment-733289396  

Build is passing https://drone.cpp.al/boostorg/json/3

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2020-11-25 00:55:20 UTC  
> Url: https://github.com/boostorg/json/pull/472#issuecomment-733394516  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/472/pullrequest-condensed-b629260.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/472/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/472/pullrequest.html)

---
