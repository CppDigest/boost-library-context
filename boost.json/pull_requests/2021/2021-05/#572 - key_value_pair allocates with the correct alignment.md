# #572 key_value_pair allocates with the correct alignment  [Merged]

> Username: grisumbras  
> Created at: 2021-05-22 14:16:49 UTC  
> Updated at: 2021-05-22 20:58:20 UTC  
> Merged at: 2021-05-22 20:57:54 UTC  
> Closed at: 2021-05-22 20:57:54 UTC  
> Url: https://github.com/boostorg/json/pull/572  



---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2021-05-22 14:28:20 UTC  
> Updated_at: 2021-05-22 18:37:48 UTC  
> Url: https://github.com/boostorg/json/pull/572#issuecomment-846415927  

# [Codecov](https://codecov.io/gh/boostorg/json/pull/572?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#572](https://codecov.io/gh/boostorg/json/pull/572?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (7b74d2a) into [develop](https://codecov.io/gh/boostorg/json/commit/6e69ed376ba16de64de77e6f91186e2b14b0ee4c?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (6e69ed3) will **not change** coverage.  
> The diff coverage is `100.00%`.  
  
> :exclamation: Current head 7b74d2a differs from pull request most recent head e62a95d. Consider uploading reports for the commit e62a95d to get more accurate results  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/json/pull/572/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/json/pull/572?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #572   +/-   ##  
========================================  
  Coverage    99.05%   99.05%             
========================================  
  Files           68       68             
  Lines         6110     6110             
========================================  
  Hits          6052     6052             
  Misses          58       58             
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/json/pull/572?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/value.hpp](https://codecov.io/gh/boostorg/json/pull/572/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL3ZhbHVlLmhwcA==) | `98.98% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/json/pull/572?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/json/pull/572?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [6e69ed3...e62a95d](https://codecov.io/gh/boostorg/json/pull/572?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).

---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2021-05-22 15:22:50 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/572#pullrequestreview-666190140  

📁 test/checking_resource.hpp

```diff
  25 |+ 
  26 |+ public:
  27 |+     explicit checking_resource(memory_resource& base) noexcept
```

> Username: vinniefalco  
> Created_at: 2021-05-22 15:22:50 UTC  
> Updated_at: 2021-05-22 15:22:51 UTC  
> Url: https://github.com/boostorg/json/pull/572#discussion_r637416400  

The parameter type should be `storage_ptr`. See:  
https://github.com/boostorg/json/blob/develop/include/boost/json/monotonic_resource.hpp#L164


---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2021-05-22 15:23:18 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/572#pullrequestreview-666190173  

📁 test/object.cpp

```diff
1500 |     {
1533 |-         counting_resource res;
1501 |+         auto& default_resource = *detail::default_resource::get();
```

> Username: vinniefalco  
> Created_at: 2021-05-22 15:23:17 UTC  
> Updated_at: 2021-05-22 15:23:18 UTC  
> Url: https://github.com/boostorg/json/pull/572#discussion_r637416445  

Tests should try as hard as possible to only use public interfaces


---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2021-05-22 15:29:03 UTC  
> Url: https://github.com/boostorg/json/pull/572#issuecomment-846423934  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/572/pullrequest-condensed-b3b2a13.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/572/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/572/pullrequest.html)

---

## Review 5 [Commented]

> Username: vinniefalco  
> Created_at: 2021-05-22 15:29:43 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/572#pullrequestreview-666190620  

📁 test/object.cpp

```diff
1508 | 
1541 |-         res = {};
1509 |+         res = checking_resource(default_resource);
```

> Username: vinniefalco  
> Created_at: 2021-05-22 15:29:42 UTC  
> Updated_at: 2021-05-22 15:30:08 UTC  
> Url: https://github.com/boostorg/json/pull/572#discussion_r637417110  

Each of these scopes should look something like this:  
```  
    {  
        auto sp = make_shared_resource<checking_resource>();  
          
        // this should also work  
        //auto sp = make_shared_resource<checking_resource>( storage_ptr() );  
  
        object o( sp );  
        o.reserve(1);  
    }  
```

> Username: vinniefalco  
> Created_at: 2021-05-22 15:31:00 UTC  
> Updated_at: 2021-05-22 15:37:30 UTC  
> Url: https://github.com/boostorg/json/pull/572#discussion_r637417262  

Alternatively, if you want a non-counted resource:  
```  
    {  
        checking_resource mr;  
          
        // this should also work  
        //checking_resource mr( storage_ptr() );  
  
        object o( &mr );  
        o.reserve(1);  
    }  
```


---

## Review 6 [Commented]

> Username: vinniefalco  
> Created_at: 2021-05-22 16:38:36 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/572#pullrequestreview-666195455  

📁 test/object.cpp

```diff
  30 |- namespace {
  31 |- 
  32 |- struct counting_resource
```

> Username: vinniefalco  
> Created_at: 2021-05-22 16:38:36 UTC  
> Url: https://github.com/boostorg/json/pull/572#discussion_r637424272  

LOL my old class was weaksauce


---

## Comment 7

> Username: cppalliance-bot  
> Created_at: 2021-05-22 17:44:05 UTC  
> Url: https://github.com/boostorg/json/pull/572#issuecomment-846441451  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/572/pullrequest-condensed-a53541a.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/572/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/572/pullrequest.html)

---

## Comment 8

> Username: cppalliance-bot  
> Created_at: 2021-05-22 20:24:59 UTC  
> Url: https://github.com/boostorg/json/pull/572#issuecomment-846459217  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/572/pullrequest-condensed-6aa9e37.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/572/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/572/pullrequest.html)

---
