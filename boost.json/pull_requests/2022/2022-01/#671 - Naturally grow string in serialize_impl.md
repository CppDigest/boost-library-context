# #671 Naturally grow string in serialize_impl [Merged]

> Username: grisumbras  
> Created at: 2022-01-15 17:15:06 UTC  
> Updated at: 2022-01-16 18:29:05 UTC  
> Merged at: 2022-01-16 17:20:48 UTC  
> Closed at: 2022-01-16 17:20:48 UTC  
> Url: https://github.com/boostorg/json/pull/671  

Fix #668

---

## Comment 1

> Username: vinniefalco  
> Created_at: 2022-01-15 17:17:01 UTC  
> Url: https://github.com/boostorg/json/pull/671#issuecomment-1013719119  

put "Fix #668" in the 3rd line of the commit msg

---

## Comment 2

> Username: grisumbras  
> Created_at: 2022-01-15 17:50:04 UTC  
> Url: https://github.com/boostorg/json/pull/671#issuecomment-1013724436  

The interesting thing about this change is that I have not been able to observe a definitive performance increase.

---

## Comment 3

> Username: vinniefalco  
> Created_at: 2022-01-15 18:18:17 UTC  
> Url: https://github.com/boostorg/json/pull/671#issuecomment-1013729561  

Well it only happens under certain circumstances, where you end up with a 1-char serialization through the loop every other trip. Which is not going to be noticeable in most cases. But the fix is correct.

---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2022-01-15 18:29:06 UTC  
> Url: https://github.com/boostorg/json/pull/671#issuecomment-1013731397  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/671/pullrequest-condensed-9835c70.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/671/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/671/pullrequest.html)

---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2022-01-16 18:29:05 UTC  
> Url: https://github.com/boostorg/json/pull/671#issuecomment-1013927672  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/671/pullrequest-condensed-54e40ee.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/671/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/671/pullrequest.html)

---
