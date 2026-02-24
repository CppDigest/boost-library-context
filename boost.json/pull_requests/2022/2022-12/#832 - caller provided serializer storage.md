# #832 caller provided serializer storage [Closed]

> Username: vinniefalco  
> Created at: 2022-12-29 01:44:02 UTC  
> Updated at: 2023-01-27 15:49:47 UTC  
> Closed at: 2023-01-27 15:27:08 UTC  
> Url: https://github.com/boostorg/json/pull/832  



---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2022-12-29 02:22:36 UTC  
> Updated_at: 2023-01-27 13:27:35 UTC  
> Url: https://github.com/boostorg/json/pull/832#issuecomment-1367030463  

# [Codecov](https://codecov.io/gh/boostorg/json/pull/832?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#832](https://codecov.io/gh/boostorg/json/pull/832?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (4fd2a03) into [develop](https://codecov.io/gh/boostorg/json/commit/607b21d827cef90233355be37054338435099074?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (607b21d) will **decrease** coverage by `0.02%`.  
> The diff coverage is `97.22%`.  
  
> :exclamation: Current head 4fd2a03 differs from pull request most recent head 011f7aa. Consider uploading reports for the commit 011f7aa to get more accurate results  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/json/pull/832/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/json/pull/832?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #832      +/-   ##  
===========================================  
- Coverage    99.00%   98.99%   -0.02%       
===========================================  
  Files           70       72       +2       
  Lines         6837     6859      +22       
===========================================  
+ Hits          6769     6790      +21       
- Misses          68       69       +1       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/json/pull/832?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/serializer.hpp](https://codecov.io/gh/boostorg/json/pull/832?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL3NlcmlhbGl6ZXIuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/json/impl/serialize.ipp](https://codecov.io/gh/boostorg/json/pull/832?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvc2VyaWFsaXplLmlwcA==) | `86.90% <81.81%> (-0.44%)` | :arrow_down: |  
| [include/boost/json/detail/impl/stack.hpp](https://codecov.io/gh/boostorg/json/pull/832?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9pbXBsL3N0YWNrLmhwcA==) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/detail/impl/stack.ipp](https://codecov.io/gh/boostorg/json/pull/832?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9pbXBsL3N0YWNrLmlwcA==) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/detail/stack.hpp](https://codecov.io/gh/boostorg/json/pull/832?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9zdGFjay5ocHA=) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/impl/serializer.ipp](https://codecov.io/gh/boostorg/json/pull/832?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvc2VyaWFsaXplci5pcHA=) | `97.01% <100.00%> (+0.03%)` | :arrow_up: |  
| [include/boost/json/array.hpp](https://codecov.io/gh/boostorg/json/pull/832?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2FycmF5LmhwcA==) | `100.00% <0.00%> (ø)` | |  
| [include/boost/json/object.hpp](https://codecov.io/gh/boostorg/json/pull/832?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL29iamVjdC5ocHA=) | `100.00% <0.00%> (ø)` | |  
| [include/boost/json/parser.hpp](https://codecov.io/gh/boostorg/json/pull/832?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL3BhcnNlci5ocHA=) | `100.00% <0.00%> (ø)` | |  
| ... and [66 more](https://codecov.io/gh/boostorg/json/pull/832?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/json/pull/832?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/json/pull/832?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [607b21d...011f7aa](https://codecov.io/gh/boostorg/json/pull/832?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2022-12-29 03:19:45 UTC  
> Url: https://github.com/boostorg/json/pull/832#issuecomment-1367046444  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/832/pullrequest-condensed-95c0c35.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/832/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/832/pullrequest.html)

---

## Comment 3

> Username: vinniefalco  
> Created_at: 2023-01-20 02:37:23 UTC  
> Url: https://github.com/boostorg/json/pull/832#issuecomment-1397855855  

Already done https://github.com/boostorg/json/pull/832

---

## Review 4 [Commented]

> Username: grisumbras  
> Created_at: 2023-01-27 10:10:29 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/832#pullrequestreview-1272318414  

📁 include/boost/json/impl/serialize.ipp

```diff
 125 | serialize(
 126 |     string_view sv)
 127 | {
```

> Username: grisumbras  
> Created_at: 2023-01-27 09:03:47 UTC  
> Updated_at: 2023-01-27 10:10:29 UTC  
> Url: https://github.com/boostorg/json/pull/832#discussion_r1088723281  

This function is never called in tests. The easiest way to fix this is to implement `serialize(string const& str)` as `return serialize( str.subview() );`  
  
But why do we even have `serialize(string const&)` ? The `string_view` overload should work for both.

> Username: vinniefalco  
> Created_at: 2023-01-27 13:07:34 UTC  
> Url: https://github.com/boostorg/json/pull/832#discussion_r1088942279  

That, I do not know.


📁 include/boost/json/detail/stack.hpp

```diff
  58 |     void
  45 |-     reserve(std::size_t n);
  59 |+     reserve(std::size_t n)
```

> Username: grisumbras  
> Created_at: 2023-01-27 09:28:01 UTC  
> Updated_at: 2023-01-27 10:10:29 UTC  
> Url: https://github.com/boostorg/json/pull/832#discussion_r1088744709  

This `reserve` function is very unusual, because unlike every other `reserve`, which checks for `n <= capacity()` this one checks for `n + size() < capacity()`. This is in fact different to the current behaviour of `detail::stack::reserve`. And while you changed the behaviour, you didn't change the one remaining invocation of the function to accomadate the difference.  
  
So, why did you change it to be that way?

---

📁 include/boost/json/detail/stack.hpp

```diff
  25 |-     char* buf_ = nullptr;
  24 |+     std::size_t size0_ = 0; // lo stack
  25 |+     std::size_t size1_ = 0; // hi stack
```

> Username: grisumbras  
> Created_at: 2023-01-27 10:09:50 UTC  
> Updated_at: 2023-01-27 10:10:29 UTC  
> Url: https://github.com/boostorg/json/pull/832#discussion_r1088786056  

I don't see where `size1_` is used


📁 include/boost/json/detail/impl/stack.hpp

```diff
  36 |+             sizeof(T) +
  37 |+             size1_);
  38 |+     std::memcpy(
```

> Username: grisumbras  
> Created_at: 2023-01-27 09:33:13 UTC  
> Updated_at: 2023-01-27 10:10:29 UTC  
> Url: https://github.com/boostorg/json/pull/832#discussion_r1088750045  

After and including this line, this function is identical to `push_unchecked`, so it should be just an invocation of it.

---

📁 include/boost/json/detail/impl/stack.hpp

```diff
  30 |+ push(T const& t)
  31 |+ {
  32 |+     if(size0_ + sizeof(T) >
```

> Username: grisumbras  
> Created_at: 2023-01-27 09:38:34 UTC  
> Updated_at: 2023-01-27 10:10:29 UTC  
> Url: https://github.com/boostorg/json/pull/832#discussion_r1088755070  

Here and elsewhere in this file. Why not store `sizeof(T)` in a `constexpr` variable instead of repeating it again and again?

> Username: vinniefalco  
> Created_at: 2023-01-27 13:02:53 UTC  
> Url: https://github.com/boostorg/json/pull/832#discussion_r1088937589  

just easier to read I guess, no having to look elsewhere. but I see your point.

---

📁 include/boost/json/detail/impl/stack.hpp

```diff
  76 |+ pop(T& t)
  77 |+ {
  78 |+     BOOST_ASSERT(
```

> Username: grisumbras  
> Created_at: 2023-01-27 09:40:19 UTC  
> Updated_at: 2023-01-27 10:10:29 UTC  
> Url: https://github.com/boostorg/json/pull/832#discussion_r1088756712  

Maybe this function should be `peek` followed by `size0 -= sizeof(T)` ?


---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2023-01-27 15:49:47 UTC  
> Url: https://github.com/boostorg/json/pull/832#issuecomment-1406683660  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/832/pullrequest-condensed-8d7ce24.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/832/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/832/pullrequest.html)

---
