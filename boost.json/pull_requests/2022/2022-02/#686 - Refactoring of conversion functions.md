# #686 Refactoring of conversion functions [Merged]

> Username: grisumbras  
> Created at: 2022-02-28 09:49:26 UTC  
> Updated at: 2022-10-19 13:48:25 UTC  
> Merged at: 2022-09-29 12:46:25 UTC  
> Closed at: 2022-09-29 12:46:25 UTC  
> Url: https://github.com/boostorg/json/pull/686  

The list of changes:  
* take advantage of mp11  
* split `container_traits` into several traits and helpers  
* split `map_traits` into several traits  
* use `value_type` more consistently  
* refactor `detail::inserter`  
* use `detail::inserter` with map-like types  
* refactor conversion of tuple-like to array  
* remove conversion from `value_ref` and don't use `value_to/from` when constructing `value` from `value_ref`  
* check for round-trip  
* user-facing conversion traits  
* opt-in null-like conversion  
* same requirements for `value_to` and `value_from`  
* stricter `is_map_like`  
* stricter `is_sequence_like`

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2022-02-28 11:23:21 UTC  
> Url: https://github.com/boostorg/json/pull/686#issuecomment-1054156837  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/686/pullrequest-condensed-19f3c16.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/686/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/686/pullrequest.html)

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2022-03-09 16:38:23 UTC  
> Url: https://github.com/boostorg/json/pull/686#issuecomment-1063122098  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/686/pullrequest-condensed-1ea5de3.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/686/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/686/pullrequest.html)

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2022-05-15 09:59:43 UTC  
> Url: https://github.com/boostorg/json/pull/686#issuecomment-1126898879  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/686/pullrequest-condensed-8625901.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/686/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/686/pullrequest.html)

---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2022-05-15 13:19:39 UTC  
> Url: https://github.com/boostorg/json/pull/686#issuecomment-1126940732  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/686/pullrequest-condensed-e1a12f4.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/686/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/686/pullrequest.html)

---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2022-08-12 14:49:39 UTC  
> Url: https://github.com/boostorg/json/pull/686#issuecomment-1213198983  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/686/pullrequest-condensed-a7d5374.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/686/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/686/pullrequest.html)

---

## Comment 6

> Username: codecov[bot]  
> Created_at: 2022-08-12 18:39:08 UTC  
> Updated_at: 2022-09-29 10:14:28 UTC  
> Url: https://github.com/boostorg/json/pull/686#issuecomment-1213408579  

# [Codecov](https://codecov.io/gh/boostorg/json/pull/686?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#686](https://codecov.io/gh/boostorg/json/pull/686?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (ce144b5) into [develop](https://codecov.io/gh/boostorg/json/commit/9fc23d689dc40d17c68177dd135caf4e93a54c75?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (9fc23d6) will **increase** coverage by `0.00%`.  
> The diff coverage is `100.00%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/json/pull/686/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/json/pull/686?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #686   +/-   ##  
========================================  
  Coverage    99.13%   99.13%             
========================================  
  Files           69       69             
  Lines         6584     6587    +3       
========================================  
+ Hits          6527     6530    +3       
  Misses          57       57             
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/json/pull/686?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/impl/value\_ref.hpp](https://codecov.io/gh/boostorg/json/pull/686/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvdmFsdWVfcmVmLmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/json/detail/value\_from.hpp](https://codecov.io/gh/boostorg/json/pull/686/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC92YWx1ZV9mcm9tLmhwcA==) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/detail/value\_to.hpp](https://codecov.io/gh/boostorg/json/pull/686/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC92YWx1ZV90by5ocHA=) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/detail/value\_traits.hpp](https://codecov.io/gh/boostorg/json/pull/686/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC92YWx1ZV90cmFpdHMuaHBw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/value\_from.hpp](https://codecov.io/gh/boostorg/json/pull/686/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL3ZhbHVlX2Zyb20uaHBw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/value\_to.hpp](https://codecov.io/gh/boostorg/json/pull/686/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL3ZhbHVlX3RvLmhwcA==) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/json/pull/686?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/json/pull/686?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [9fc23d6...ce144b5](https://codecov.io/gh/boostorg/json/pull/686?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 7

> Username: cppalliance-bot  
> Created_at: 2022-08-12 19:34:43 UTC  
> Url: https://github.com/boostorg/json/pull/686#issuecomment-1213451093  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/686/pullrequest-condensed-ac53146.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/686/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/686/pullrequest.html)

---

## Comment 8

> Username: cppalliance-bot  
> Created_at: 2022-08-13 09:54:44 UTC  
> Url: https://github.com/boostorg/json/pull/686#issuecomment-1214126054  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/686/pullrequest-condensed-1742b0a.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/686/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/686/pullrequest.html)

---

## Comment 9

> Username: cppalliance-bot  
> Created_at: 2022-08-28 13:40:55 UTC  
> Url: https://github.com/boostorg/json/pull/686#issuecomment-1229459405  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/686/pullrequest-condensed-93108aa.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/686/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/686/pullrequest.html)

---

## Comment 10

> Username: cppalliance-bot  
> Created_at: 2022-09-05 17:19:40 UTC  
> Url: https://github.com/boostorg/json/pull/686#issuecomment-1237326049  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/686/pullrequest-condensed-caf4da1.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/686/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/686/pullrequest.html)

---

## Comment 11

> Username: cppalliance-bot  
> Created_at: 2022-09-16 13:16:28 UTC  
> Url: https://github.com/boostorg/json/pull/686#issuecomment-1249353641  

An automated preview of the documentation is available at [https://686.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://686.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 12

> Username: cppalliance-bot  
> Created_at: 2022-09-16 13:56:27 UTC  
> Url: https://github.com/boostorg/json/pull/686#issuecomment-1249397353  

An automated preview of the documentation is available at [https://686.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://686.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 13

> Username: cppalliance-bot  
> Created_at: 2022-09-16 14:21:29 UTC  
> Url: https://github.com/boostorg/json/pull/686#issuecomment-1249426246  

An automated preview of the documentation is available at [https://686.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://686.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 14

> Username: cppalliance-bot  
> Created_at: 2022-09-16 15:29:40 UTC  
> Url: https://github.com/boostorg/json/pull/686#issuecomment-1249506179  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/686/pullrequest-condensed-e8f200e.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/686/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/686/pullrequest.html)

---

## Comment 15

> Username: cppalliance-bot  
> Created_at: 2022-09-17 13:30:41 UTC  
> Url: https://github.com/boostorg/json/pull/686#issuecomment-1250072189  

An automated preview of the documentation is available at [https://686.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://686.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 16

> Username: cppalliance-bot  
> Created_at: 2022-09-17 15:46:14 UTC  
> Url: https://github.com/boostorg/json/pull/686#issuecomment-1250093645  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/686/pullrequest-condensed-0a1ba2d.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/686/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/686/pullrequest.html)

---

## Comment 17

> Username: cppalliance-bot  
> Created_at: 2022-09-23 02:21:47 UTC  
> Url: https://github.com/boostorg/json/pull/686#issuecomment-1255735401  

An automated preview of the documentation is available at [https://686.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://686.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 18

> Username: cppalliance-bot  
> Created_at: 2022-09-23 03:29:39 UTC  
> Url: https://github.com/boostorg/json/pull/686#issuecomment-1255764463  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/686/pullrequest-condensed-8f85160.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/686/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/686/pullrequest.html)

---

## Comment 19

> Username: cppalliance-bot  
> Created_at: 2022-09-26 09:36:46 UTC  
> Url: https://github.com/boostorg/json/pull/686#issuecomment-1257764500  

An automated preview of the documentation is available at [https://686.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://686.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 20

> Username: cppalliance-bot  
> Created_at: 2022-09-26 10:44:39 UTC  
> Url: https://github.com/boostorg/json/pull/686#issuecomment-1257841194  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/686/pullrequest-condensed-229218d.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/686/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/686/pullrequest.html)

---

## Comment 21

> Username: cppalliance-bot  
> Created_at: 2022-09-29 09:06:27 UTC  
> Url: https://github.com/boostorg/json/pull/686#issuecomment-1261990151  

An automated preview of the documentation is available at [https://686.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://686.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 22

> Username: cppalliance-bot  
> Created_at: 2022-09-29 09:21:31 UTC  
> Url: https://github.com/boostorg/json/pull/686#issuecomment-1262008498  

An automated preview of the documentation is available at [https://686.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://686.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 23

> Username: cppalliance-bot  
> Created_at: 2022-09-29 09:36:29 UTC  
> Url: https://github.com/boostorg/json/pull/686#issuecomment-1262025695  

An automated preview of the documentation is available at [https://686.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://686.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 24

> Username: cppalliance-bot  
> Created_at: 2022-09-29 09:56:28 UTC  
> Url: https://github.com/boostorg/json/pull/686#issuecomment-1262048604  

An automated preview of the documentation is available at [https://686.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://686.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 25

> Username: cppalliance-bot  
> Created_at: 2022-09-29 10:06:29 UTC  
> Url: https://github.com/boostorg/json/pull/686#issuecomment-1262059935  

An automated preview of the documentation is available at [https://686.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://686.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 26

> Username: cppalliance-bot  
> Created_at: 2022-09-29 11:14:34 UTC  
> Url: https://github.com/boostorg/json/pull/686#issuecomment-1262132138  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/686/pullrequest-condensed-1ca210b.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/686/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/686/pullrequest.html)

---

## Review 27 [Commented]

> Username: vinniefalco  
> Created_at: 2022-09-29 19:40:19 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/686#pullrequestreview-1125898240  

📁 include/boost/json/conversion.hpp

```diff
  17 |+ #include <utility>
  18 |+ 
  19 |+ BOOST_JSON_NS_BEGIN
```

> Username: vinniefalco  
> Created_at: 2022-09-29 19:40:19 UTC  
> Url: https://github.com/boostorg/json/pull/686#discussion_r983954264  

if we don't have standalone we don't need this namespace macro

> Username: vinniefalco  
> Created_at: 2022-09-29 19:43:34 UTC  
> Url: https://github.com/boostorg/json/pull/686#discussion_r983957125  

All public header files need to be listed in `<boost/json.hpp>`, this one is missing (are any other new files missing?)


---

## Review 28 [Commented]

> Username: vinniefalco  
> Created_at: 2022-09-29 19:40:46 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/686#pullrequestreview-1125898769  

📁 include/boost/json/conversion.hpp

```diff
  18 |+ 
  19 |+ BOOST_JSON_NS_BEGIN
  20 |+ namespace detail {
```

> Username: vinniefalco  
> Created_at: 2022-09-29 19:40:45 UTC  
> Updated_at: 2022-09-29 19:40:46 UTC  
> Url: https://github.com/boostorg/json/pull/686#discussion_r983954879  

Implementation details should be in json/detail or json/impl not a public header since this is scanned by doxygen

> Username: vinniefalco  
> Created_at: 2022-09-29 19:53:20 UTC  
> Updated_at: 2022-09-29 19:53:21 UTC  
> Url: https://github.com/boostorg/json/pull/686#discussion_r983964440  

This stuff should go in `<boost/json/detail/conversion.hpp>`


---

## Review 29 [Commented]

> Username: vinniefalco  
> Created_at: 2022-09-29 19:41:36 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/686#pullrequestreview-1125899625  

📁 include/boost/json/conversion.hpp

```diff
 155 |+ #endif
 156 |+ 
 157 |+ /** Determine if `T` can be treated like a 1-to-1 mapping during
```

> Username: vinniefalco  
> Created_at: 2022-09-29 19:41:36 UTC  
> Url: https://github.com/boostorg/json/pull/686#discussion_r983955647  

Metafunction briefs should start with "Metafunction"

> Username: grisumbras  
> Created_at: 2022-09-29 20:14:51 UTC  
> Url: https://github.com/boostorg/json/pull/686#discussion_r983981695  

These aren't metafunctions, they are type traits.

> Username: vinniefalco  
> Created_at: 2022-09-29 20:21:15 UTC  
> Updated_at: 2022-09-29 20:21:25 UTC  
> Url: https://github.com/boostorg/json/pull/686#discussion_r983989363  

it isn't a function taking a type as an argument and returning a value?

> Username: grisumbras  
> Created_at: 2022-10-19 13:48:25 UTC  
> Url: https://github.com/boostorg/json/pull/686#discussion_r999480250  

Metafunctions return types, these return boolean values, so they are type traits.


---

## Review 30 [Commented]

> Username: vinniefalco  
> Created_at: 2022-09-29 19:42:22 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/686#pullrequestreview-1125900457  

📁 include/boost/json/conversion.hpp

```diff
 173 |+ 
 174 |+     then the trait provides the member constant `value`
 175 |+     that is equal to `true`. Otherwise, `value` is equal to `false`.<br>
```

> Username: vinniefalco  
> Created_at: 2022-09-29 19:42:22 UTC  
> Updated_at: 2022-09-29 19:44:30 UTC  
> Url: https://github.com/boostorg/json/pull/686#discussion_r983956270  

The alternatives should be "Then this is an alias for `std::true_type`,...  otherwise it is an alias for `std::false_type`" or some variation of this text

> Username: grisumbras  
> Created_at: 2022-09-29 20:18:21 UTC  
> Url: https://github.com/boostorg/json/pull/686#discussion_r983984441  

They aren't aliases, they are separate types. I copied the approach to documenting these from cppreference.


---

## Review 31 [Commented]

> Username: vinniefalco  
> Created_at: 2022-09-29 19:47:26 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/686#pullrequestreview-1125905907  

📁 include/boost/json/conversion.hpp

```diff
 204 |+ */
 205 |+ template<class T, class Enable = void>
 206 |+ struct is_map_like
```

> Username: vinniefalco  
> Created_at: 2022-09-29 19:47:26 UTC  
> Url: https://github.com/boostorg/json/pull/686#discussion_r983960141  

I usually document metafunctions like this  
```  
#ifdef BOOST_JSON_DOCS  
template<class T>  
using has_value_to = __see_below__;  
#else  
...  
```  
  
The result looks like this:  
![image](https://user-images.githubusercontent.com/1503976/193127697-d62be541-8b85-44c7-8eaf-7da701d36d3a.png)

> Username: grisumbras  
> Created_at: 2022-09-29 20:19:33 UTC  
> Updated_at: 2022-09-29 20:19:34 UTC  
> Url: https://github.com/boostorg/json/pull/686#discussion_r983985963  

They are not aliases, they are class templates. This is a deliberate decision to allow user specialisations.

> Username: vinniefalco  
> Created_at: 2022-09-29 20:22:03 UTC  
> Updated_at: 2022-09-29 20:22:04 UTC  
> Url: https://github.com/boostorg/json/pull/686#discussion_r983990339  

Oh, yes if they can be specialized then `using` is not correct


---

## Review 32 [Commented]

> Username: vinniefalco  
> Created_at: 2022-09-29 19:48:26 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/686#pullrequestreview-1125906933  

📁 include/boost/json/detail/value_to.hpp

```diff
 196 |-                 map_traits<T>::pair_value_type>(val.value())});
 217 |+     detail::try_reserve(result, obj.size(), reserve_implementation<T>());
 218 |+     std::transform(obj.begin(), obj.end(),
```

> Username: vinniefalco  
> Created_at: 2022-09-29 19:48:26 UTC  
> Url: https://github.com/boostorg/json/pull/686#discussion_r983960805  

Does this mean we are including `<algorithm>` now?

> Username: grisumbras  
> Created_at: 2022-09-29 20:23:49 UTC  
> Url: https://github.com/boostorg/json/pull/686#discussion_r983992437  

We were already including it


---

## Review 33 [Commented]

> Username: vinniefalco  
> Created_at: 2022-09-29 19:52:02 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/686#pullrequestreview-1125910771  

📁 test/value_to.cpp

```diff
 147 |+         {
 148 |+             int n;
 149 |+             detail::try_reserve(
```

> Username: vinniefalco  
> Created_at: 2022-09-29 19:52:01 UTC  
> Updated_at: 2022-09-29 19:52:02 UTC  
> Url: https://github.com/boostorg/json/pull/686#discussion_r983963453  

You're testing an implementation detail directly, which is not something the user would ever do. Better to test this indirectly

> Username: grisumbras  
> Created_at: 2022-09-29 20:26:38 UTC  
> Url: https://github.com/boostorg/json/pull/686#discussion_r983994561  

This is specifically a test for these implementation details. Indirect testing would be much more complicated, and probably a bit brittle, because this helpers are simply for memory allocation optimization purposes.


---

## Review 34 [Commented]

> Username: vinniefalco  
> Created_at: 2022-09-29 19:52:45 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/686#pullrequestreview-1125911618  

📁 doc/qbk/quickref.xml

```diff
  60 |+           <member><link linkend="json.ref.boost__json__is_map_like">is_map_like</link></member>
  61 |+           <member><link linkend="json.ref.boost__json__is_null_like">is_null_like</link></member>
  62 |+           <member><link linkend="json.ref.boost__json__is_tuple_like">is_tuple_like</link></member>
```

> Username: vinniefalco  
> Created_at: 2022-09-29 19:52:45 UTC  
> Url: https://github.com/boostorg/json/pull/686#discussion_r983964036  

These are not in alphabetical order, was this intended?

> Username: grisumbras  
> Created_at: 2022-09-29 20:27:04 UTC  
> Updated_at: 2022-09-29 20:27:05 UTC  
> Url: https://github.com/boostorg/json/pull/686#discussion_r983994885  

No, it was a mistake


---

## Comment 35

> Username: vinniefalco  
> Created_at: 2022-09-29 19:53:57 UTC  
> Url: https://github.com/boostorg/json/pull/686#issuecomment-1262744156  

This needs exposition in the docs

---

## Review 36 [Commented]

> Username: vinniefalco  
> Created_at: 2022-09-29 19:59:56 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/686#pullrequestreview-1125919776  

📁 include/boost/json/conversion.hpp

```diff
 238 |+ 
 239 |+ 
 240 |+     @par Types satisfying the trait
```

> Username: vinniefalco  
> Created_at: 2022-09-29 19:59:56 UTC  
> Url: https://github.com/boostorg/json/pull/686#discussion_r983969435  

these are called models, e.g. `@par Models`

> Username: grisumbras  
> Created_at: 2022-09-29 20:28:52 UTC  
> Updated_at: 2022-09-29 20:28:53 UTC  
> Url: https://github.com/boostorg/json/pull/686#discussion_r983996142  

No, because this is not a concept or a type requirement. I've asked around and people say that "satifies trait" is correct.


---

## Comment 37

> Username: vinniefalco  
> Created_at: 2022-09-29 20:04:51 UTC  
> Url: https://github.com/boostorg/json/pull/686#issuecomment-1262755809  

@pdimov did you review this?

---
