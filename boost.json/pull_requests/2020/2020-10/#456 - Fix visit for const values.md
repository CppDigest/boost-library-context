# #456 Fix visit for const values [Merged]

> Username: madmongo1  
> Created at: 2020-10-22 06:45:32 UTC  
> Updated at: 2020-10-22 23:10:00 UTC  
> Merged at: 2020-10-22 23:10:00 UTC  
> Closed at: 2020-10-22 23:10:00 UTC  
> Url: https://github.com/boostorg/json/pull/456  



---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2020-10-22 08:03:41 UTC  
> Url: https://github.com/boostorg/json/pull/456#issuecomment-714311015  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/456/pullrequest-condensed-3e5cb1e.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/456/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/456/pullrequest.html)

---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2020-10-22 17:38:55 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/456#pullrequestreview-514969340  

📁 test/visit.cpp

```diff
  51 |+         {
  52 |+             json::value v = value(nullptr);
  53 |+             BOOST_TEST(visit(check_mutable{kind::null}, v));
```

> Username: vinniefalco  
> Created_at: 2020-10-22 17:38:55 UTC  
> Updated_at: 2020-10-22 18:55:02 UTC  
> Url: https://github.com/boostorg/json/pull/456#discussion_r510342377  

Why did we bloat the number of lines?

> Username: madmongo1  
> Created_at: 2020-10-22 18:10:03 UTC  
> Updated_at: 2020-10-22 18:55:02 UTC  
> Url: https://github.com/boostorg/json/pull/456#discussion_r510360766  

Because we have to prove the correct path is taken when passing lvalues.  
Previously the test was passing rvalues.

> Username: vinniefalco  
> Created_at: 2020-10-22 22:41:32 UTC  
> Url: https://github.com/boostorg/json/pull/456#discussion_r510498231  

Yepp... I see what you mean now :)


---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2020-10-22 20:08:50 UTC  
> Url: https://github.com/boostorg/json/pull/456#issuecomment-714732630  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/456/pullrequest-condensed-87674b0.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/456/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/456/pullrequest.html)

---
