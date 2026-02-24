# #522 Add std::hash specializations for json types [Merged]

> Username: doganulus  
> Created at: 2021-03-06 11:50:05 UTC  
> Updated at: 2021-05-07 21:48:35 UTC  
> Merged at: 2021-05-06 21:20:39 UTC  
> Closed at: 2021-05-06 21:20:39 UTC  
> Url: https://github.com/boostorg/json/pull/522  

As discussed for the issue #521, this pull request  
  
* Add std::hash specializations for `json::array`, `json::object`, and  `json::value`.  
* Add a new header `boost/json/detail/hash_combine.hpp` for inline hash functions.  
* Add basic unit tests

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2021-03-06 13:04:07 UTC  
> Url: https://github.com/boostorg/json/pull/522#issuecomment-791934828  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/522/pullrequest-condensed-bfcf9cf.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/522/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/522/pullrequest.html)

---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2021-03-06 14:51:47 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/522#pullrequestreview-605762069  

📁 include/boost/json/impl/array.ipp

```diff
 769 |+   std::size_t seed = ja.size();
 770 |+   for (const auto& jv : ja) {
 771 |+     seed = boost::json::detail::hash_combine(
```

> Username: vinniefalco  
> Created_at: 2021-03-06 14:51:47 UTC  
> Updated_at: 2021-05-06 18:37:53 UTC  
> Url: https://github.com/boostorg/json/pull/522#discussion_r588890911  

`::boost::json::detail::hash_combine` ?

> Username: pdimov  
> Created_at: 2021-03-06 15:40:49 UTC  
> Updated_at: 2021-05-06 18:37:53 UTC  
> Url: https://github.com/boostorg/json/pull/522#discussion_r588896084  

I don't think there's any need to open `namespace std` here.

> Username: doganulus  
> Created_at: 2021-03-06 17:37:18 UTC  
> Updated_at: 2021-05-06 18:37:53 UTC  
> Url: https://github.com/boostorg/json/pull/522#discussion_r588908691  

Once a user had reported me a GCC bug (affecting from GCC4.8 to 7) related to such specializations:  
https://gcc.gnu.org/bugzilla/show_bug.cgi?id=56480  
  
The quick solution for that is to open the `namespace std`.

> Username: pdimov  
> Created_at: 2021-03-06 18:02:40 UTC  
> Updated_at: 2021-05-06 18:37:53 UTC  
> Url: https://github.com/boostorg/json/pull/522#discussion_r588911544  

That's not the same. You're defining the member function here, not specializing the class template.

> Username: doganulus  
> Created_at: 2021-03-09 15:40:03 UTC  
> Updated_at: 2021-05-06 18:37:53 UTC  
> Url: https://github.com/boostorg/json/pull/522#discussion_r590481364  

Done.


---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2021-03-06 14:54:19 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/522#pullrequestreview-605762233  

📁 include/boost/json/impl/object.ipp

```diff
 837 |+     std::size_t seed = jo.size();
 838 |+     for (const auto& kv_pair : jo) {
 839 |+         const auto hk = std::hash<boost::json::string>{}(kv_pair.key());
```

> Username: vinniefalco  
> Created_at: 2021-03-06 14:54:19 UTC  
> Updated_at: 2021-05-06 18:37:53 UTC  
> Url: https://github.com/boostorg/json/pull/522#discussion_r588891119  

This constructs a temporary `json::string` from `key()` (which is a `string_view`). I don't think that's what you intended :)

> Username: pdimov  
> Created_at: 2021-03-06 15:41:42 UTC  
> Updated_at: 2021-05-06 18:37:53 UTC  
> Url: https://github.com/boostorg/json/pull/522#discussion_r588896149  

Unfortunately `std::hash<std::string_view>` is C++17. :-)

> Username: vinniefalco  
> Created_at: 2021-03-06 17:18:36 UTC  
> Updated_at: 2021-05-06 18:37:53 UTC  
> Url: https://github.com/boostorg/json/pull/522#discussion_r588906700  

Use  
```  
        auto const hk = ::boost::json::detail::digest(  
            kv_pair.key().data(), kv_pair.key().size(), 0);  
```

> Username: doganulus  
> Created_at: 2021-03-09 15:32:26 UTC  
> Updated_at: 2021-05-06 18:37:53 UTC  
> Url: https://github.com/boostorg/json/pull/522#discussion_r590474617  

Done.


---

## Review 4 [Commented]

> Username: vinniefalco  
> Created_at: 2021-03-06 14:55:48 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/522#pullrequestreview-605762331  

📁 include/boost/json/impl/value.ipp

```diff
 507 |+     ::boost::json::value const& jv) const noexcept 
 508 |+ {
 509 |+   std::size_t seed = static_cast<std::size_t>(jv.kind());
```

> Username: vinniefalco  
> Created_at: 2021-03-06 14:55:48 UTC  
> Updated_at: 2021-05-06 18:37:53 UTC  
> Url: https://github.com/boostorg/json/pull/522#discussion_r588891275  

Should `hash( jv )` == `hash( jv.get_array() )` if `jv.is_array()==true`? With the current code, they will not be equal. I'm not sure if they should be equal or not. @pdimov ?

> Username: pdimov  
> Created_at: 2021-03-06 15:38:16 UTC  
> Updated_at: 2021-05-06 18:37:53 UTC  
> Url: https://github.com/boostorg/json/pull/522#discussion_r588895815  

That's a change I requested. Variants typically hash the kind. E.g. the hash of variant<string, string> when it holds the same string as alternative 0 or alternative 1 will differ (which matches what operator== does.)

> Username: pdimov  
> Created_at: 2021-03-06 15:44:12 UTC  
> Updated_at: 2021-05-06 18:37:53 UTC  
> Url: https://github.com/boostorg/json/pull/522#discussion_r588896400  

But, as I also said, this as written doesn't match the behavior of value::operator==, where int64 and uint64 are considered equal when they hold the same value.

> Username: pdimov  
> Created_at: 2021-03-06 15:46:14 UTC  
> Updated_at: 2021-05-06 18:37:53 UTC  
> Url: https://github.com/boostorg/json/pull/522#discussion_r588896625  

Scratch that, the code further on overrides the seed in the uint64 case. :-) However it assumes that std::hash<int64_t> and std::hash<std::uint64_t> produce the same value, which is not guaranteed.

> Username: vinniefalco  
> Created_at: 2021-03-06 16:15:20 UTC  
> Updated_at: 2021-05-06 18:37:53 UTC  
> Url: https://github.com/boostorg/json/pull/522#discussion_r588899732  

Should `operator==` always return false when comparing int64 to uint64?

> Username: pdimov  
> Created_at: 2021-03-06 16:59:56 UTC  
> Updated_at: 2021-05-06 18:37:53 UTC  
> Url: https://github.com/boostorg/json/pull/522#discussion_r588904758  

I don't think there's any pressing need to change the current equality behavior at this time.

> Username: doganulus  
> Created_at: 2021-03-07 14:45:07 UTC  
> Updated_at: 2021-05-06 18:37:53 UTC  
> Url: https://github.com/boostorg/json/pull/522#discussion_r589040341  

Question: What is the opinion regarding the equality check with different types (including primitives)?   
  
For example, these expressions all evaluate to `true`:  
  
```cpp  
value(nullptr) == nullptr  
value(true) == true  
value(false) == false  
value(13) == 13  
value({1,2,3}) == array({1,2,3})  
value({{"a", 1}, {"b",2}}) == object({{"a", 1}, {"b",2}})   
```  
as right hand sides are converted to `value` by `operator==`.

> Username: vinniefalco  
> Created_at: 2021-03-07 18:32:35 UTC  
> Updated_at: 2021-05-06 18:37:53 UTC  
> Url: https://github.com/boostorg/json/pull/522#discussion_r589069471  

That could in theory be quite expensive, can you please open an issue for this in particular?


---

## Review 5 [Commented]

> Username: vinniefalco  
> Created_at: 2021-03-06 14:58:21 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/522#pullrequestreview-605762522  

📁 test/value.cpp

```diff
2116 |+ 
2117 |+         BOOST_TEST(harr0 == harr1);
2118 |+         BOOST_TEST(harr1 != harr2);
```

> Username: vinniefalco  
> Created_at: 2021-03-06 14:58:21 UTC  
> Updated_at: 2021-05-06 18:37:53 UTC  
> Url: https://github.com/boostorg/json/pull/522#discussion_r588891593  

Nice test coverage, but lets be honest here - there would have been a small but non zero chance that these hashes collided :)

> Username: pdimov  
> Created_at: 2021-03-06 15:52:18 UTC  
> Updated_at: 2021-05-06 18:37:53 UTC  
> Url: https://github.com/boostorg/json/pull/522#discussion_r588897323  

2^-32 isn't bad.  
  
I'd have written this differently - with a helper function that when v1 == v2 checks that the hashes match, and when v1 != v2 checks that they differ, so as to test the correspondence between op== and the hash. Or with two helper functions, one checking the equal case and one checking the unequal case.  
  
Or I suppose one can just check equality inline.  
  
Things like 0 != false, {} != 0, [] != 0, {} != [], [] != [[]], etc should be checked, too. The above doesn't check across kinds (except for null).

> Username: doganulus  
> Created_at: 2021-03-06 17:38:11 UTC  
> Updated_at: 2021-05-06 18:37:53 UTC  
> Url: https://github.com/boostorg/json/pull/522#discussion_r588908854  

Will update tests accordingly.


---

## Review 6 [Commented]

> Username: vinniefalco  
> Created_at: 2021-03-06 14:58:58 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/522#pullrequestreview-605762572  

📁 test/value.cpp

```diff
2141 |         testInitList();
2142 |         testEquality();
2143 |+         testHash();
```

> Username: vinniefalco  
> Created_at: 2021-03-06 14:58:58 UTC  
> Updated_at: 2021-05-06 18:37:53 UTC  
> Url: https://github.com/boostorg/json/pull/522#discussion_r588891657  

array.cpp and object.cpp each need their own small `testHash()` to exercise the corresponding hash function.


---

## Comment 7

> Username: vinniefalco  
> Created_at: 2021-03-06 15:08:41 UTC  
> Url: https://github.com/boostorg/json/pull/522#issuecomment-791969037  

Couple of comments but this looks great, thanks !

---

## Comment 8

> Username: cppalliance-bot  
> Created_at: 2021-03-06 15:34:03 UTC  
> Url: https://github.com/boostorg/json/pull/522#issuecomment-791973293  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/522/pullrequest-condensed-2fe445e.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/522/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/522/pullrequest.html)

---

## Comment 9

> Username: codecov[bot]  
> Created_at: 2021-03-07 18:49:25 UTC  
> Updated_at: 2021-05-06 20:54:50 UTC  
> Url: https://github.com/boostorg/json/pull/522#issuecomment-792332157  

# [Codecov](https://codecov.io/gh/boostorg/json/pull/522?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#522](https://codecov.io/gh/boostorg/json/pull/522?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (7f43f07) into [develop](https://codecov.io/gh/boostorg/json/commit/7c482b5ee0bcc7f7f8ff1dc6aaa1b55a2304be73?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (7c482b5) will **increase** coverage by `0.00%`.  
> The diff coverage is `100.00%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/json/pull/522/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/json/pull/522?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #522   +/-   ##  
========================================  
  Coverage    99.10%   99.11%             
========================================  
  Files           67       68    +1       
  Lines         6058     6104   +46       
========================================  
+ Hits          6004     6050   +46       
  Misses          54       54             
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/json/pull/522?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/array.hpp](https://codecov.io/gh/boostorg/json/pull/522/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2FycmF5LmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/json/object.hpp](https://codecov.io/gh/boostorg/json/pull/522/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL29iamVjdC5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/json/value.hpp](https://codecov.io/gh/boostorg/json/pull/522/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL3ZhbHVlLmhwcA==) | `99.79% <ø> (ø)` | |  
| [include/boost/json/detail/hash\_combine.hpp](https://codecov.io/gh/boostorg/json/pull/522/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9oYXNoX2NvbWJpbmUuaHBw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/impl/array.ipp](https://codecov.io/gh/boostorg/json/pull/522/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvYXJyYXkuaXBw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/impl/object.ipp](https://codecov.io/gh/boostorg/json/pull/522/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvb2JqZWN0LmlwcA==) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/impl/value.ipp](https://codecov.io/gh/boostorg/json/pull/522/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvdmFsdWUuaXBw) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/json/pull/522?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/json/pull/522?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [7c482b5...7f43f07](https://codecov.io/gh/boostorg/json/pull/522?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).

---

## Comment 10

> Username: cppalliance-bot  
> Created_at: 2021-03-09 16:43:58 UTC  
> Url: https://github.com/boostorg/json/pull/522#issuecomment-794143256  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/522/pullrequest-condensed-0befaa6.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/522/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/522/pullrequest.html)

---

## Comment 11

> Username: vinniefalco  
> Created_at: 2021-03-09 19:55:05 UTC  
> Url: https://github.com/boostorg/json/pull/522#issuecomment-794375017  

This can't go in until after the current Boost release cycle is over, which will be after 1.76.0 ships in the first week of April.

---

## Comment 12

> Username: cppalliance-bot  
> Created_at: 2021-03-09 21:26:57 UTC  
> Url: https://github.com/boostorg/json/pull/522#issuecomment-794476635  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/522/pullrequest-condensed-dec3b01.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/522/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/522/pullrequest.html)

---

## Comment 13

> Username: doganulus  
> Created_at: 2021-05-05 10:38:24 UTC  
> Url: https://github.com/boostorg/json/pull/522#issuecomment-832588370  

What about this feature? Could be merged now?

---

## Comment 14

> Username: grisumbras  
> Created_at: 2021-05-06 16:37:35 UTC  
> Url: https://github.com/boostorg/json/pull/522#issuecomment-833672486  

Please, rebase on develop and push again. Meanwhile by the end of the week I'll review the PR.

---

## Comment 15

> Username: cppalliance-bot  
> Created_at: 2021-05-06 19:49:02 UTC  
> Url: https://github.com/boostorg/json/pull/522#issuecomment-833814140  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/522/pullrequest-condensed-e90771e.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/522/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/522/pullrequest.html)

---

## Comment 16

> Username: grisumbras  
> Created_at: 2021-05-06 21:22:08 UTC  
> Url: https://github.com/boostorg/json/pull/522#issuecomment-833876417  

Thanks for your contribution!

---

## Comment 17

> Username: pdimov  
> Created_at: 2021-05-07 20:52:58 UTC  
> Url: https://github.com/boostorg/json/pull/522#issuecomment-834772195  

Might be a good idea to add `hash_value` overloads while we're at it (so that `boost::hash` works.) These can just return the result of `std::hash`, or vice versa.

---

## Comment 18

> Username: vinniefalco  
> Created_at: 2021-05-07 21:48:35 UTC  
> Url: https://github.com/boostorg/json/pull/522#issuecomment-834803357  

> Might be a good idea to add hash_value overloads while we're at it  
  
Open a new issue?

---
