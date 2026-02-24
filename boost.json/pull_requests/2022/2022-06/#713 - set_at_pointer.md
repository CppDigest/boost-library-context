# #713 set_at_pointer [Merged]

> Username: grisumbras  
> Created at: 2022-06-03 12:21:43 UTC  
> Updated at: 2023-01-02 12:01:15 UTC  
> Merged at: 2023-01-02 10:43:31 UTC  
> Closed at: 2023-01-02 10:43:31 UTC  
> Url: https://github.com/boostorg/json/pull/713  

Fix #700  
  
This implementation makes these choices:  
1. Nulls are replaced when creating intermediaries.  
2. When creating intermediaries segments are always treated as object keys, (that is only objects are created as intermediaries).  
3. Arrays are extended by 1 element at most.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-06-03 12:41:39 UTC  
> Updated_at: 2023-01-02 10:23:22 UTC  
> Url: https://github.com/boostorg/json/pull/713#issuecomment-1145923899  

# [Codecov](https://codecov.io/gh/boostorg/json/pull/713?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#713](https://codecov.io/gh/boostorg/json/pull/713?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (36cb433) into [develop](https://codecov.io/gh/boostorg/json/commit/c6508328c8b9dfd62ab83e812b85ea9518e6a438?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (c650832) will **increase** coverage by `0.13%`.  
> The diff coverage is `100.00%`.  
  
> :exclamation: Current head 36cb433 differs from pull request most recent head a210032. Consider uploading reports for the commit a210032 to get more accurate results  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/json/pull/713/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/json/pull/713?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #713      +/-   ##  
===========================================  
+ Coverage    99.00%   99.13%   +0.13%       
===========================================  
  Files           70       69       -1       
  Lines         6802     6613     -189       
===========================================  
- Hits          6734     6556     -178       
+ Misses          68       57      -11       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/json/pull/713?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/value.hpp](https://codecov.io/gh/boostorg/json/pull/713/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL3ZhbHVlLmhwcA==) | `98.91% <ø> (-0.05%)` | :arrow_down: |  
| [include/boost/json/value\_ref.hpp](https://codecov.io/gh/boostorg/json/pull/713/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL3ZhbHVlX3JlZi5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/json/impl/pointer.ipp](https://codecov.io/gh/boostorg/json/pull/713/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvcG9pbnRlci5pcHA=) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/array.hpp](https://codecov.io/gh/boostorg/json/pull/713/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2FycmF5LmhwcA==) | `100.00% <0.00%> (ø)` | |  
| [include/boost/json/object.hpp](https://codecov.io/gh/boostorg/json/pull/713/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL29iamVjdC5ocHA=) | `100.00% <0.00%> (ø)` | |  
| [include/boost/json/string.hpp](https://codecov.io/gh/boostorg/json/pull/713/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL3N0cmluZy5ocHA=) | `100.00% <0.00%> (ø)` | |  
| [include/boost/json/value\_to.hpp](https://codecov.io/gh/boostorg/json/pull/713/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL3ZhbHVlX3RvLmhwcA==) | `100.00% <0.00%> (ø)` | |  
| [include/boost/json/impl/array.hpp](https://codecov.io/gh/boostorg/json/pull/713/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvYXJyYXkuaHBw) | `100.00% <0.00%> (ø)` | |  
| [include/boost/json/impl/array.ipp](https://codecov.io/gh/boostorg/json/pull/713/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvYXJyYXkuaXBw) | `100.00% <0.00%> (ø)` | |  
| ... and [22 more](https://codecov.io/gh/boostorg/json/pull/713/diff?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/json/pull/713?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/json/pull/713?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [c650832...a210032](https://codecov.io/gh/boostorg/json/pull/713?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2022-06-03 15:19:33 UTC  
> Url: https://github.com/boostorg/json/pull/713#issuecomment-1146069277  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/713/pullrequest-condensed-f894d04.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/713/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/713/pullrequest.html)

---

## Comment 3

> Username: vinniefalco  
> Created_at: 2022-06-08 22:07:33 UTC  
> Url: https://github.com/boostorg/json/pull/713#issuecomment-1150463666  

Why did performance change

---

## Comment 4

> Username: grisumbras  
> Created_at: 2022-06-09 09:54:44 UTC  
> Url: https://github.com/boostorg/json/pull/713#issuecomment-1150918480  

Mystery to me too

---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2022-06-21 20:44:34 UTC  
> Url: https://github.com/boostorg/json/pull/713#issuecomment-1162332237  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/713/pullrequest-condensed-5768e03.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/713/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/713/pullrequest.html)

---

## Comment 6

> Username: cppalliance-bot  
> Created_at: 2022-06-21 21:01:30 UTC  
> Url: https://github.com/boostorg/json/pull/713#issuecomment-1162357110  

An automated preview of the documentation is available at [https://713.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://713.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 7

> Username: cppalliance-bot  
> Created_at: 2022-06-21 21:16:37 UTC  
> Url: https://github.com/boostorg/json/pull/713#issuecomment-1162375885  

An automated preview of the documentation is available at [https://713.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://713.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 8

> Username: cppalliance-bot  
> Created_at: 2022-06-21 22:24:33 UTC  
> Url: https://github.com/boostorg/json/pull/713#issuecomment-1162424363  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/713/pullrequest-condensed-b13df1b.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/713/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/713/pullrequest.html)

---

## Comment 9

> Username: cppalliance-bot  
> Created_at: 2022-07-10 18:26:24 UTC  
> Url: https://github.com/boostorg/json/pull/713#issuecomment-1179776519  

An automated preview of the documentation is available at [https://713.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://713.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 10

> Username: cppalliance-bot  
> Created_at: 2022-07-10 19:34:43 UTC  
> Url: https://github.com/boostorg/json/pull/713#issuecomment-1179785574  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/713/pullrequest-condensed-cafb406.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/713/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/713/pullrequest.html)

---

## Comment 11

> Username: cppalliance-bot  
> Created_at: 2022-08-03 20:06:32 UTC  
> Url: https://github.com/boostorg/json/pull/713#issuecomment-1204421528  

An automated preview of the documentation is available at [https://713.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://713.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 12

> Username: cppalliance-bot  
> Created_at: 2022-08-03 20:21:34 UTC  
> Url: https://github.com/boostorg/json/pull/713#issuecomment-1204435020  

An automated preview of the documentation is available at [https://713.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://713.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 13

> Username: cppalliance-bot  
> Created_at: 2022-08-03 21:29:39 UTC  
> Url: https://github.com/boostorg/json/pull/713#issuecomment-1204503593  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/713/pullrequest-condensed-52c2ff7.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/713/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/713/pullrequest.html)

---

## Comment 14

> Username: cppalliance-bot  
> Created_at: 2022-08-04 07:56:30 UTC  
> Url: https://github.com/boostorg/json/pull/713#issuecomment-1204900060  

An automated preview of the documentation is available at [https://713.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://713.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 15

> Username: cppalliance-bot  
> Created_at: 2022-08-04 09:04:39 UTC  
> Url: https://github.com/boostorg/json/pull/713#issuecomment-1204974945  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/713/pullrequest-condensed-54896a0.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/713/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/713/pullrequest.html)

---

## Comment 16

> Username: vinniefalco  
> Created_at: 2022-08-13 14:04:01 UTC  
> Url: https://github.com/boostorg/json/pull/713#issuecomment-1214164907  

Kind of a long section name though  
![image](https://user-images.githubusercontent.com/1503976/184497701-d1da81d8-4f17-438c-8bc1-73ce6422557d.png)

---

## Review 17 [Commented]

> Username: vinniefalco  
> Created_at: 2022-08-13 14:06:03 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/713#pullrequestreview-1071967029  

📁 include/boost/json/impl/pointer.ipp

```diff
 394 | 
 395 |+ value&
 396 |+ value::set_at_pointer(string_view ptr, value_ref jv)
```

> Username: vinniefalco  
> Created_at: 2022-08-13 14:06:03 UTC  
> Url: https://github.com/boostorg/json/pull/713#discussion_r945152266  

`jv` is the canonical parameter name for `value`.  
  
`value_ref` is not that.


---

## Review 18 [Commented]

> Username: vinniefalco  
> Created_at: 2022-08-13 14:06:50 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/713#pullrequestreview-1071967089  

📁 include/boost/json/impl/pointer.ipp

```diff
 399 |+     auto const result = try_set_at_pointer(ptr, jv, ec);
 400 |+     if( ec )
 401 |+         detail::throw_system_error(ec, BOOST_JSON_SOURCE_POS);
```

> Username: vinniefalco  
> Created_at: 2022-08-13 14:06:49 UTC  
> Updated_at: 2022-08-13 14:06:50 UTC  
> Url: https://github.com/boostorg/json/pull/713#discussion_r945152323  

these parameters could be defaulted in the declaration, e.g.  
```  
BOOST_URL_DECL void BOOST_NORETURN throw_length_error(char const* what,  
    source_location const& loc = BOOST_CURRENT_LOCATION);  
```


---

## Review 19 [Commented]

> Username: vinniefalco  
> Created_at: 2022-08-13 14:07:32 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/713#pullrequestreview-1071967153  

📁 include/boost/json/impl/pointer.ipp

```diff
 429 |+             if( ec == error::past_the_end || index == arr.size() )
 430 |+             {
 431 |+                 ec.clear();
```

> Username: vinniefalco  
> Created_at: 2022-08-13 14:07:32 UTC  
> Url: https://github.com/boostorg/json/pull/713#discussion_r945152390  

`ec = {}` is canonical


---

## Review 20 [Commented]

> Username: vinniefalco  
> Created_at: 2022-08-13 14:12:34 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/713#pullrequestreview-1071967584  

📁 include/boost/json/value.hpp

```diff
3326 |+ 
3327 |+         @par Exception Safety
3328 |+         Basic guarantee.
```

> Username: vinniefalco  
> Created_at: 2022-08-13 14:12:34 UTC  
> Url: https://github.com/boostorg/json/pull/713#discussion_r945152956  

but we could offer the Strong Guarantee, no?  If we do two passes through the pointer?

> Username: grisumbras  
> Created_at: 2022-11-15 18:33:01 UTC  
> Url: https://github.com/boostorg/json/pull/713#discussion_r1023136064  

No, it's almost impossible, because we create elements as we go. The complexity of undoing things would be ridiculous.


---

## Review 21 [Commented]

> Username: vinniefalco  
> Created_at: 2022-08-13 14:13:12 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/713#pullrequestreview-1071967629  

📁 include/boost/json/value.hpp

```diff
3336 |+         @see
3337 |+         <a href="https://datatracker.ietf.org/doc/html/rfc6901">
3338 |+             RFC 6901 - JavaScript Object Notation (JSON) Pointer</a>
```

> Username: vinniefalco  
> Created_at: 2022-08-13 14:13:12 UTC  
> Updated_at: 2022-08-13 14:13:54 UTC  
> Url: https://github.com/boostorg/json/pull/713#discussion_r945153009  

I format it this way  
```  
        @li <a href="https://datatracker.ietf.org/doc/html/rfc6901"  
            >JavaScript Object Notation (JSON) Pointer (rfc6901)</a>  
```


---

## Comment 22

> Username: vinniefalco  
> Created_at: 2022-08-13 14:14:59 UTC  
> Url: https://github.com/boostorg/json/pull/713#issuecomment-1214166370  

If it doesn't already say so, the javadoc / exposition for `value_ref` should explain that objects of that type should only be declared as function parameters. Never as class members, local variables, or objects with static storage duration.

---

## Comment 23

> Username: vinniefalco  
> Created_at: 2022-08-13 14:17:14 UTC  
> Url: https://github.com/boostorg/json/pull/713#issuecomment-1214166709  

The decision to only create objects along the way seems unintuitive and controversial. What do other implementations of JSON Pointer do? What is the rationale for this?

---

## Review 24 [Commented]

> Username: vinniefalco  
> Created_at: 2022-08-13 15:24:55 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/713#pullrequestreview-1071973217  

📁 include/boost/json/impl/pointer.ipp

```diff
 422 |+             value val(object(obj.storage()), obj.storage());
 423 |+             key_value_pair kv(key, pilfer(val));
 424 |+             obj.reserve(obj.size() + 1);
```

> Username: vinniefalco  
> Created_at: 2022-08-13 15:24:55 UTC  
> Url: https://github.com/boostorg/json/pull/713#discussion_r945159874  

`insert_impl` already reserves first, you dont need to do it

> Username: vinniefalco  
> Created_at: 2022-08-13 15:42:48 UTC  
> Url: https://github.com/boostorg/json/pull/713#discussion_r945161547  

What do you think of this  
```  
    object tmp( { { string_view(token), object_kind } }, obj.storage() );  
    obj.swap( tmp );  
    return obj.begin()  
```


---

## Comment 25

> Username: vinniefalco  
> Created_at: 2022-08-13 15:30:38 UTC  
> Url: https://github.com/boostorg/json/pull/713#issuecomment-1214177235  

This PR is not ready... needs work :)

---

## Review 26 [Commented]

> Username: vinniefalco  
> Created_at: 2022-11-13 21:54:12 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/713#pullrequestreview-1178303217  

📁 doc/qbk/03_08_nested_access.qbk

```diff
  11 | 
  12 |- [section:nested_access Accessing Deeply Nested Elements]
  12 |+ [section:nested_access Accessing and Changing Deeply Nested Elements]
```

> Username: vinniefalco  
> Created_at: 2022-11-13 21:54:12 UTC  
> Url: https://github.com/boostorg/json/pull/713#discussion_r1020969796  

Quite a long section name this will appear in the toc?


---

## Review 27 [Commented]

> Username: vinniefalco  
> Created_at: 2022-11-13 21:54:26 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/713#pullrequestreview-1178303247  

📁 doc/qbk/03_08_nested_access.qbk

```diff
  34 |+ __at_pointer__, but additionally, if it encounters a `null` value it replaces
  35 |+ it with an __object__, and when the referenced object or __array__ element
  36 |+ doesn't exist, it creates an object. A version of that function that reports
```

> Username: vinniefalco  
> Created_at: 2022-11-13 21:54:26 UTC  
> Url: https://github.com/boostorg/json/pull/713#discussion_r1020969818  

That's a lot of commas


---

## Review 28 [Commented]

> Username: vinniefalco  
> Created_at: 2022-11-13 21:55:28 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/713#pullrequestreview-1178303362  

📁 include/boost/json/pointer.hpp

```diff
  17 |+ 
  18 |+ std::pair<std::size_t, string_view>
  19 |+ parse_pointer_number_segment(
```

> Username: vinniefalco  
> Created_at: 2022-11-13 21:55:27 UTC  
> Updated_at: 2022-11-13 21:55:28 UTC  
> Url: https://github.com/boostorg/json/pull/713#discussion_r1020969915  

This is the name of a public function?

> Username: grisumbras  
> Created_at: 2022-11-14 09:14:12 UTC  
> Url: https://github.com/boostorg/json/pull/713#discussion_r1021243253  

Yes. It's a helper that users need to implement their own behaviour while traversing pointers.


---

## Review 29 [Commented]

> Username: vinniefalco  
> Created_at: 2022-11-13 21:55:54 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/713#pullrequestreview-1178303414  

📁 include/boost/json/value.hpp

```diff
  37 |+ struct basic_pointer_traverse_state;
  38 |+ using pointer_traverse_state = basic_pointer_traverse_state< false >;
  39 |+ using const_pointer_traverse_state = basic_pointer_traverse_state< true >;
```

> Username: vinniefalco  
> Created_at: 2022-11-13 21:55:54 UTC  
> Url: https://github.com/boostorg/json/pull/713#discussion_r1020969979  

These are public?

> Username: grisumbras  
> Created_at: 2022-11-14 09:14:40 UTC  
> Url: https://github.com/boostorg/json/pull/713#discussion_r1021243811  

Yes


---

## Review 30 [Commented]

> Username: vinniefalco  
> Created_at: 2022-11-13 21:56:25 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/713#pullrequestreview-1178303469  

📁 include/boost/json/value.hpp

```diff
3381 |+     step_over_pointer(string_view ptr) const noexcept;
3382 |+ 
3383 |+     inline
```

> Username: vinniefalco  
> Created_at: 2022-11-13 21:56:25 UTC  
> Url: https://github.com/boostorg/json/pull/713#discussion_r1020970074  

the `inline` goes on the definition not the declaration


---

## Review 31 [Commented]

> Username: vinniefalco  
> Created_at: 2022-11-13 22:03:18 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/713#pullrequestreview-1178304225  

📁 include/boost/json/value.hpp

```diff
3379 |+     BOOST_JSON_DECL
3380 |+     const_pointer_traverse_state
3381 |+     step_over_pointer(string_view ptr) const noexcept;
```

> Username: vinniefalco  
> Created_at: 2022-11-13 22:03:18 UTC  
> Url: https://github.com/boostorg/json/pull/713#discussion_r1020971001  

`ptr` is misleading, the convention in my libraries is to name the variable based on its type:  
  
`jv` json::value  
`s` string or string_view  
`ec` error code  
  
Only very rarely are things named after what they are, e.g. `string_view key`. But `ptr` is very suggestive of a pointer which it is not. This is misleading


---

## Comment 32

> Username: cppalliance-bot  
> Created_at: 2022-11-13 22:54:39 UTC  
> Url: https://github.com/boostorg/json/pull/713#issuecomment-1312843772  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/713/pullrequest-condensed-756bd41.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/713/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/713/pullrequest.html)

---

## Comment 33

> Username: cppalliance-bot  
> Created_at: 2022-11-14 13:39:40 UTC  
> Url: https://github.com/boostorg/json/pull/713#issuecomment-1313727795  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/713/pullrequest-condensed-988bf44.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/713/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/713/pullrequest.html)

---

## Review 34 [Commented]

> Username: vinniefalco  
> Created_at: 2022-11-14 14:41:02 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/713#pullrequestreview-1179235526  

📁 test/pointer.cpp

```diff
  38 |+ 
  39 |+         assert( state.error.failed() );
  40 |+         if( state.error == json::error::past_the_end )
```

> Username: vinniefalco  
> Created_at: 2022-11-14 14:41:02 UTC  
> Url: https://github.com/boostorg/json/pull/713#discussion_r1021625532  

Why isn't this simply `error::end`?


---

## Review 35 [Approved]

> Username: pdimov  
> Created_at: 2022-11-15 18:40:43 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/json/pull/713#pullrequestreview-1181380207  

This is what we've been talking about, yes. Everything looks fine at a quick glance.

---

## Comment 36

> Username: cppalliance-bot  
> Created_at: 2022-11-15 19:44:39 UTC  
> Url: https://github.com/boostorg/json/pull/713#issuecomment-1315785820  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/713/pullrequest-condensed-4aee578.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/713/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/713/pullrequest.html)

---

## Comment 37

> Username: cppalliance-bot  
> Created_at: 2022-11-16 11:59:41 UTC  
> Url: https://github.com/boostorg/json/pull/713#issuecomment-1316891346  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/713/pullrequest-condensed-e60ba68.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/713/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/713/pullrequest.html)

---

## Comment 38

> Username: cppalliance-bot  
> Created_at: 2023-01-02 10:31:35 UTC  
> Url: https://github.com/boostorg/json/pull/713#issuecomment-1368818010  

An automated preview of the documentation is available at [https://713.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://713.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 39

> Username: cppalliance-bot  
> Created_at: 2023-01-02 12:01:14 UTC  
> Url: https://github.com/boostorg/json/pull/713#issuecomment-1368883865  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/713/pullrequest-condensed-c9a0aa7.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/713/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/713/pullrequest.html)

---
