# #505 Simple parse fix [Merged]

> Username: grisumbras  
> Created at: 2021-02-25 05:53:17 UTC  
> Updated at: 2021-03-10 11:30:40 UTC  
> Merged at: 2021-03-01 19:30:48 UTC  
> Closed at: 2021-03-01 19:30:48 UTC  
> Url: https://github.com/boostorg/json/pull/505  

Fixes #484. ~Also maybe fixes # 353.~

---

## Review 1 [Commented]

> Username: grisumbras  
> Created_at: 2021-02-25 05:56:41 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/505#pullrequestreview-598218574  

📁 include/boost/json/basic_parser_impl.hpp

```diff
 110 |+ 
 111 |+     if(e < -308)
 112 |+         return x * 0.0;
```

> Username: grisumbras  
> Created_at: 2021-02-25 05:56:41 UTC  
> Updated_at: 2021-03-01 11:12:41 UTC  
> Url: https://github.com/boostorg/json/pull/505#discussion_r582564325  

I don't know if division by infinity can trap on some platforms/configurations. If it doesn't, this check can be removed.

> Username: vinniefalco  
> Created_at: 2021-02-25 18:38:15 UTC  
> Updated_at: 2021-03-01 11:12:41 UTC  
> Url: https://github.com/boostorg/json/pull/505#discussion_r583074192  

I don't understand this line, why not just `return 0` ?

> Username: grisumbras  
> Created_at: 2021-02-25 18:39:57 UTC  
> Updated_at: 2021-03-01 11:12:41 UTC  
> Url: https://github.com/boostorg/json/pull/505#discussion_r583075236  

Are negative zeros important?

> Username: grisumbras  
> Created_at: 2021-02-25 18:41:53 UTC  
> Updated_at: 2021-03-01 11:12:41 UTC  
> Url: https://github.com/boostorg/json/pull/505#discussion_r583076481  

Actually, I think,  the whole block can be removed. This will result in `x / INFINITY`.


---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2021-02-25 07:08:49 UTC  
> Url: https://github.com/boostorg/json/pull/505#issuecomment-785669832  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/505/pullrequest-condensed-7a3a40b.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/505/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/505/pullrequest.html)

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2021-02-27 15:08:54 UTC  
> Url: https://github.com/boostorg/json/pull/505#issuecomment-787087580  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/505/pullrequest-condensed-2e2aaf3.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/505/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/505/pullrequest.html)

---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2021-03-01 12:23:50 UTC  
> Url: https://github.com/boostorg/json/pull/505#issuecomment-787909193  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/505/pullrequest-condensed-1c74522.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/505/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/505/pullrequest.html)

---

## Comment 5

> Username: grisumbras  
> Created_at: 2021-03-01 13:06:34 UTC  
> Url: https://github.com/boostorg/json/pull/505#issuecomment-787934931  

@vinniefalco this can be merged now.

---

## Comment 6

> Username: jano42  
> Created_at: 2021-03-02 05:24:33 UTC  
> Updated_at: 2021-03-02 05:24:56 UTC  
> Url: https://github.com/boostorg/json/pull/505#issuecomment-788604743  

@vinniefalco, @grisumbras  : thanks ;-)

---
