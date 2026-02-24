# #792 test conversion to string_view [Closed]

> Username: vinniefalco  
> Created at: 2022-10-27 16:19:06 UTC  
> Updated at: 2022-11-06 11:05:01 UTC  
> Closed at: 2022-11-06 11:03:14 UTC  
> Url: https://github.com/boostorg/json/pull/792  



---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2022-10-27 19:16:01 UTC  
> Url: https://github.com/boostorg/json/pull/792#issuecomment-1293957886  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/792/pullrequest-condensed-d9bfce5.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/792/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/792/pullrequest.html)

---

## Comment 2

> Username: grisumbras  
> Created_at: 2022-11-06 11:05:01 UTC  
> Url: https://github.com/boostorg/json/pull/792#issuecomment-1304774985  

This is a problem with GCC 8.3 that doesn't have a solution on our side (users will have to upgrade). In our code we should just avoid using conversion from `string` to `string_view` and use `string::subview` instead.

---
