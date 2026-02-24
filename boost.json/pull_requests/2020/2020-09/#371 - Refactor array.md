# #371 Refactor array [Closed]

> Username: vinniefalco  
> Created at: 2020-09-20 16:41:48 UTC  
> Updated at: 2020-09-27 22:10:00 UTC  
> Closed at: 2020-09-20 20:39:54 UTC  
> Url: https://github.com/boostorg/json/pull/371  

Less code, faster

---

## Review 1 [Commented]

> Username: sdkrystian  
> Created_at: 2020-09-20 17:07:39 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/371#pullrequestreview-492206910  

📁 include/boost/json/array.hpp

```diff
  93 |- 
  84 |     friend class value;
  85 |+     struct header;
```

> Username: sdkrystian  
> Created_at: 2020-09-20 16:45:15 UTC  
> Updated_at: 2020-09-20 19:46:13 UTC  
> Url: https://github.com/boostorg/json/pull/371#discussion_r491709505  

`header` isn't used anywhere


📁 include/boost/json/impl/array.hpp

```diff
  25 |- public:
  26 |-     bool commit = false;
  29 |+     char pad_[sizeof(value) - (
```

> Username: sdkrystian  
> Created_at: 2020-09-20 16:47:40 UTC  
> Updated_at: 2020-09-20 19:46:13 UTC  
> Url: https://github.com/boostorg/json/pull/371#discussion_r491709708  

Since `table` is aligned to `value`, we don't need `pad_`

> Username: vinniefalco  
> Created_at: 2020-09-20 17:09:18 UTC  
> Updated_at: 2020-09-20 19:46:13 UTC  
> Url: https://github.com/boostorg/json/pull/371#discussion_r491711631  

yeah that's the theory but my static asserts fail without it


📁 include/boost/json/impl/array.ipp

```diff
 252 |+     do
 253 |+     {
 254 |+         ::new(p++) value(sp_);
```

> Username: sdkrystian  
> Created_at: 2020-09-20 17:04:29 UTC  
> Updated_at: 2020-09-20 19:46:13 UTC  
> Url: https://github.com/boostorg/json/pull/371#discussion_r491711241  

Something that has applications outside of just `array` (you might not like this because it would be private interface): we could just `memcpy` the `storage_ptr` and at the end just have a single branch which checks `is_counted`, and if that returns `true` we increment the ref-count by `count`. It would kill a branch in each construction (thus optimizing the non-counted case) and we would only have to do one atomic add instead of `count` (atomic adds are expensive -- `lock add QWORD PTR[rax], 1` takes 3x as many cycles as `add QWORD PTR[rax], 1`), which would optimize the counted case.

> Username: sdkrystian  
> Created_at: 2020-09-20 17:06:44 UTC  
> Updated_at: 2020-09-20 19:46:13 UTC  
> Url: https://github.com/boostorg/json/pull/371#discussion_r491711456  

This would especially help in `value_stack`. Since we don't do deep copies, we can check `is_counted` and increment the ref-counter only when `push_array`, `push_object` and `release` are called.


---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2020-09-20 17:31:27 UTC  
> Url: https://github.com/boostorg/json/pull/371#issuecomment-695813722  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/371/pullrequest-condensed-8552298.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/371/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/371/pullrequest.html)

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2020-09-20 18:16:59 UTC  
> Url: https://github.com/boostorg/json/pull/371#issuecomment-695818465  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/371/pullrequest-condensed-f1301ae.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/371/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/371/pullrequest.html)

---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2020-09-20 19:02:31 UTC  
> Url: https://github.com/boostorg/json/pull/371#issuecomment-695823251  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/371/pullrequest-condensed-d130558.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/371/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/371/pullrequest.html)

---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2020-09-20 19:48:00 UTC  
> Url: https://github.com/boostorg/json/pull/371#issuecomment-695828076  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/371/pullrequest-condensed-7ccd737.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/371/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/371/pullrequest.html)

---

## Comment 6

> Username: vinniefalco  
> Created_at: 2020-09-20 20:39:54 UTC  
> Url: https://github.com/boostorg/json/pull/371#issuecomment-695833577  

Merged

---

## Comment 7

> Username: cppalliance-bot  
> Created_at: 2020-09-20 20:48:30 UTC  
> Url: https://github.com/boostorg/json/pull/371#issuecomment-695834554  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/371/pullrequest-condensed-22de345.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/371/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/371/pullrequest.html)

---
