# #298 Handler specifies structure and string limits [Closed]

> Username: sdkrystian  
> Created at: 2020-09-07 18:30:42 UTC  
> Updated at: 2020-09-09 23:39:41 UTC  
> Closed at: 2020-09-09 23:39:41 UTC  
> Url: https://github.com/boostorg/json/pull/298  



---

## Review 1 [Commented]

> Username: vinniefalco  
> Created_at: 2020-09-07 18:31:39 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/298#pullrequestreview-483685699  

📁 example/validate.cpp

```diff
  30 |- validate( string_view s )
  29 |+ // The null parser discards all the data
  30 |+ class null_parser
```

> Username: vinniefalco  
> Created_at: 2020-09-07 18:31:39 UTC  
> Updated_at: 2020-09-09 19:10:46 UTC  
> Url: https://github.com/boostorg/json/pull/298#discussion_r484541642  

Can't put it in the function?


---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2020-09-07 18:33:21 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/298#pullrequestreview-483685988  

📁 include/boost/json/detail/array_impl.hpp

```diff
  59 |-         return BOOST_JSON_MAX_STRUCTURED_SIZE;
  60 |-     }
  57 |+     max_size() noexcept;
```

> Username: vinniefalco  
> Created_at: 2020-09-07 18:33:21 UTC  
> Updated_at: 2020-09-09 19:10:46 UTC  
> Url: https://github.com/boostorg/json/pull/298#discussion_r484541915  

are constexpr functions inline by default?

> Username: sdkrystian  
> Created_at: 2020-09-07 18:35:48 UTC  
> Updated_at: 2020-09-09 19:10:46 UTC  
> Url: https://github.com/boostorg/json/pull/298#discussion_r484542319  

Yup


---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2020-09-07 18:34:41 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/298#pullrequestreview-483686225  

📁 include/boost/json/parser.hpp

```diff
 111 |+         static
 112 |+         std::size_t
 113 |+         max_object_size = object::max_size();
```

> Username: vinniefalco  
> Created_at: 2020-09-07 18:34:41 UTC  
> Updated_at: 2020-09-09 19:10:46 UTC  
> Url: https://github.com/boostorg/json/pull/298#discussion_r484542111  

Consider  
```  
constexpr static std::size_t  
    max_object_size = object::max_size();  
```  
since it is private


---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2020-09-07 19:40:49 UTC  
> Url: https://github.com/boostorg/json/pull/298#issuecomment-688487464  

![pullrequest](http://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/298/pullrequest-condensed.png)  
Benchmark test results. More info at [http://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/298/pullrequest.html](http://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/298/pullrequest.html)

---

## Comment 5

> Username: sdkrystian  
> Created_at: 2020-09-07 20:07:50 UTC  
> Url: https://github.com/boostorg/json/pull/298#issuecomment-688495254  

@sdarwin I think I see the problem now -- we should be comparing against the commit prior to the first commit on a PR instead of comparing to the current develop

---

## Comment 6

> Username: cppalliance-bot  
> Created_at: 2020-09-07 20:58:32 UTC  
> Url: https://github.com/boostorg/json/pull/298#issuecomment-688509063  

![pullrequest](http://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/298/pullrequest-condensed.png)  
Benchmark test results. More info at [http://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/298/pullrequest.html](http://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/298/pullrequest.html)

---

## Comment 7

> Username: cppalliance-bot  
> Created_at: 2020-09-07 22:59:22 UTC  
> Url: https://github.com/boostorg/json/pull/298#issuecomment-688533884  

![pullrequest](http://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/298/pullrequest-condensed.png)  
Benchmark test results. More info at [http://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/298/pullrequest.html](http://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/298/pullrequest.html)

---

## Comment 8

> Username: cppalliance-bot  
> Created_at: 2020-09-08 04:21:00 UTC  
> Url: https://github.com/boostorg/json/pull/298#issuecomment-688609123  

![pullrequest](http://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/298/pullrequest-condensed.png)  
Benchmark test results. More info at [http://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/298/pullrequest.html](http://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/298/pullrequest.html)

---

## Review 9 [Commented]

> Username: vinniefalco  
> Created_at: 2020-09-09 18:50:21 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/298#pullrequestreview-485303153  

📁 include/boost/json/basic_parser.hpp

```diff
 973 |-             error::key_too_large : error::string_too_large);
 970 |+     if(IsKey)
 971 |+     {
```

> Username: vinniefalco  
> Created_at: 2020-09-09 18:50:20 UTC  
> Updated_at: 2020-09-09 19:10:46 UTC  
> Url: https://github.com/boostorg/json/pull/298#discussion_r485839258  

```  
BOOST_ASSERT(total <= Handler::max_key_size_)  
```


---

## Comment 10

> Username: cppalliance-bot  
> Created_at: 2020-09-09 22:44:58 UTC  
> Url: https://github.com/boostorg/json/pull/298#issuecomment-689861800  

![pullrequest](http://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/298/pullrequest-condensed-9665f9a.png)  
Benchmark test results. More info at [http://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/298/pullrequest.html](http://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/298/pullrequest.html)

---
