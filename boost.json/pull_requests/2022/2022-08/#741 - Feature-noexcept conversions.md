# #741 Feature/noexcept conversions [Merged]

> Username: grisumbras  
> Created at: 2022-08-24 14:42:21 UTC  
> Updated at: 2022-10-14 17:13:59 UTC  
> Merged at: 2022-10-14 17:13:44 UTC  
> Closed at: 2022-10-14 17:13:44 UTC  
> Url: https://github.com/boostorg/json/pull/741  

Fix #736  
Fix #667

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2022-08-24 14:51:31 UTC  
> Url: https://github.com/boostorg/json/pull/741#issuecomment-1225834736  

An automated preview of the documentation is available at [https://741.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://741.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2022-08-24 14:52:59 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/741#pullrequestreview-1083965186  

📁 include/boost/json/error.hpp

```diff
 111 |+ 
 112 |+     /// JSON string was expected during conversion
 113 |+     string_expected,
```

> Username: vinniefalco  
> Created_at: 2022-08-24 14:52:58 UTC  
> Updated_at: 2022-08-24 14:52:59 UTC  
> Url: https://github.com/boostorg/json/pull/741#discussion_r953905968  

when you add errors these need to be added to error.ipp and error.cpp (tests) as well

> Username: grisumbras  
> Created_at: 2022-08-24 14:54:47 UTC  
> Updated_at: 2022-08-24 14:54:48 UTC  
> Url: https://github.com/boostorg/json/pull/741#discussion_r953908146  

Don't review this, it's just an outline


---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2022-08-24 14:54:27 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/741#pullrequestreview-1083967720  

📁 include/boost/json/system_error.hpp

```diff
  31 | 
  32 |+ template< class T >
  33 |+ using result = boost::system::result<T>;
```

> Username: vinniefalco  
> Created_at: 2022-08-24 14:54:27 UTC  
> Url: https://github.com/boostorg/json/pull/741#discussion_r953907764  

Boost.JSON should follow the model used in Boost.URL. You can probably just copy this file:  
https://github.com/CPPAlliance/url/blob/develop/include/boost/url/error_types.hpp  
  
and change the existing JSON headers to include this file instead. Keep the original JSON headers if they overlap and just edit them to only include this new file.


---

## Review 4 [Commented]

> Username: vinniefalco  
> Created_at: 2022-08-24 14:55:26 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/741#pullrequestreview-1083969431  

📁 include/boost/json/system_error.hpp

```diff
  30 | using system_error = boost::system::system_error;
  31 | 
  32 |+ template< class T >
```

> Username: vinniefalco  
> Created_at: 2022-08-24 14:55:26 UTC  
> Url: https://github.com/boostorg/json/pull/741#discussion_r953908952  

You have to put a javadoc on this and add it to the reference :) See next note


---

## Comment 5

> Username: codecov[bot]  
> Created_at: 2022-08-24 14:55:36 UTC  
> Updated_at: 2022-10-14 14:23:10 UTC  
> Url: https://github.com/boostorg/json/pull/741#issuecomment-1225839759  

# [Codecov](https://codecov.io/gh/boostorg/json/pull/741?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#741](https://codecov.io/gh/boostorg/json/pull/741?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (a8ccda2) into [develop](https://codecov.io/gh/boostorg/json/commit/76de28556a9075fe81d271551e14fdbe9a08fe65?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (76de285) will **decrease** coverage by `0.12%`.  
> The diff coverage is `94.25%`.  
  
> :exclamation: Current head a8ccda2 differs from pull request most recent head 0d8a14c. Consider uploading reports for the commit 0d8a14c to get more accurate results  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/json/pull/741/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/json/pull/741?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #741      +/-   ##  
===========================================  
- Coverage    98.96%   98.84%   -0.13%       
===========================================  
  Files           69       69                
  Lines         6589     6733     +144       
===========================================  
+ Hits          6521     6655     +134       
- Misses          68       78      +10       
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/json/pull/741?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/detail/value\_traits.hpp](https://codecov.io/gh/boostorg/json/pull/741/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC92YWx1ZV90cmFpdHMuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/json/impl/value\_ref.hpp](https://codecov.io/gh/boostorg/json/pull/741/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvdmFsdWVfcmVmLmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/json/detail/value\_to.hpp](https://codecov.io/gh/boostorg/json/pull/741/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC92YWx1ZV90by5ocHA=) | `94.76% <93.78%> (-5.24%)` | :arrow_down: |  
| [include/boost/json/impl/error.ipp](https://codecov.io/gh/boostorg/json/pull/741/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvZXJyb3IuaXBw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/value\_to.hpp](https://codecov.io/gh/boostorg/json/pull/741/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL3ZhbHVlX3RvLmhwcA==) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/basic\_parser\_impl.hpp](https://codecov.io/gh/boostorg/json/pull/741/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2Jhc2ljX3BhcnNlcl9pbXBsLmhwcA==) | `98.33% <0.00%> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/json/pull/741?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/json/pull/741?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [76de285...0d8a14c](https://codecov.io/gh/boostorg/json/pull/741?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Review 6 [Commented]

> Username: vinniefalco  
> Created_at: 2022-08-24 14:55:54 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/741#pullrequestreview-1083970237  

📁 test/doc_forward_conversion.cpp

```diff
 179 |+ 
 180 |+ //[doc_forward_conversion_3_2
 181 |+         auto const rc = try_value_to<thirdparty::customer>( jv );
```

> Username: vinniefalco  
> Created_at: 2022-08-24 14:55:54 UTC  
> Url: https://github.com/boostorg/json/pull/741#discussion_r953909546  

we should avoid `auto` in examples


---

## Review 7 [Commented]

> Username: vinniefalco  
> Created_at: 2022-08-24 14:56:20 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/741#pullrequestreview-1083970998  

📁 test/doc_forward_conversion.cpp

```diff
 183 |+ 
 184 |+         auto const rv = try_value_from( *rc );
 185 |+         BOOST_TEST( rv.has_value() );
```

> Username: vinniefalco  
> Created_at: 2022-08-24 14:56:20 UTC  
> Url: https://github.com/boostorg/json/pull/741#discussion_r953910115  

Are you showing `BOOST_TEST` in examples?


---

## Comment 8

> Username: cppalliance-bot  
> Created_at: 2022-08-24 15:54:44 UTC  
> Url: https://github.com/boostorg/json/pull/741#issuecomment-1225915262  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/741/pullrequest-condensed-96e3e57.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/741/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/741/pullrequest.html)

---

## Comment 9

> Username: cppalliance-bot  
> Created_at: 2022-09-03 13:16:31 UTC  
> Url: https://github.com/boostorg/json/pull/741#issuecomment-1236118828  

An automated preview of the documentation is available at [https://741.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://741.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 10

> Username: cppalliance-bot  
> Created_at: 2022-09-03 14:24:42 UTC  
> Url: https://github.com/boostorg/json/pull/741#issuecomment-1236129972  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/741/pullrequest-condensed-83e6bd4.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/741/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/741/pullrequest.html)

---

## Comment 11

> Username: cppalliance-bot  
> Created_at: 2022-09-17 13:26:24 UTC  
> Url: https://github.com/boostorg/json/pull/741#issuecomment-1250071531  

An automated preview of the documentation is available at [https://741.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://741.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 12

> Username: cppalliance-bot  
> Created_at: 2022-09-17 14:34:44 UTC  
> Url: https://github.com/boostorg/json/pull/741#issuecomment-1250082271  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/741/pullrequest-condensed-d96585f.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/741/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/741/pullrequest.html)

---

## Comment 13

> Username: cppalliance-bot  
> Created_at: 2022-09-21 14:41:36 UTC  
> Url: https://github.com/boostorg/json/pull/741#issuecomment-1253812106  

An automated preview of the documentation is available at [https://741.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://741.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 14

> Username: cppalliance-bot  
> Created_at: 2022-09-21 15:49:39 UTC  
> Url: https://github.com/boostorg/json/pull/741#issuecomment-1253898941  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/741/pullrequest-condensed-fc23d88.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/741/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/741/pullrequest.html)

---

## Comment 15

> Username: cppalliance-bot  
> Created_at: 2022-09-26 11:06:52 UTC  
> Url: https://github.com/boostorg/json/pull/741#issuecomment-1257864076  

An automated preview of the documentation is available at [https://741.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://741.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 16

> Username: cppalliance-bot  
> Created_at: 2022-09-26 12:09:34 UTC  
> Url: https://github.com/boostorg/json/pull/741#issuecomment-1257935494  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/741/pullrequest-condensed-d2e548c.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/741/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/741/pullrequest.html)

---

## Comment 17

> Username: cppalliance-bot  
> Created_at: 2022-09-29 12:51:39 UTC  
> Url: https://github.com/boostorg/json/pull/741#issuecomment-1262232900  

An automated preview of the documentation is available at [https://741.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://741.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 18

> Username: cppalliance-bot  
> Created_at: 2022-09-29 13:59:32 UTC  
> Url: https://github.com/boostorg/json/pull/741#issuecomment-1262327381  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/741/pullrequest-condensed-e4f4533.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/741/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/741/pullrequest.html)

---

## Comment 19

> Username: cppalliance-bot  
> Created_at: 2022-09-30 08:51:30 UTC  
> Url: https://github.com/boostorg/json/pull/741#issuecomment-1263296537  

An automated preview of the documentation is available at [https://741.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://741.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 20

> Username: cppalliance-bot  
> Created_at: 2022-09-30 09:21:31 UTC  
> Url: https://github.com/boostorg/json/pull/741#issuecomment-1263327916  

An automated preview of the documentation is available at [https://741.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://741.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 21

> Username: cppalliance-bot  
> Created_at: 2022-09-30 09:46:30 UTC  
> Url: https://github.com/boostorg/json/pull/741#issuecomment-1263352867  

An automated preview of the documentation is available at [https://741.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://741.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 22

> Username: cppalliance-bot  
> Created_at: 2022-09-30 10:54:36 UTC  
> Url: https://github.com/boostorg/json/pull/741#issuecomment-1263424813  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/741/pullrequest-condensed-0f417b6.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/741/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/741/pullrequest.html)

---

## Comment 23

> Username: cppalliance-bot  
> Created_at: 2022-09-30 12:51:33 UTC  
> Url: https://github.com/boostorg/json/pull/741#issuecomment-1263535347  

An automated preview of the documentation is available at [https://741.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://741.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 24

> Username: cppalliance-bot  
> Created_at: 2022-09-30 12:56:32 UTC  
> Url: https://github.com/boostorg/json/pull/741#issuecomment-1263540732  

An automated preview of the documentation is available at [https://741.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://741.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 25

> Username: cppalliance-bot  
> Created_at: 2022-09-30 13:21:38 UTC  
> Url: https://github.com/boostorg/json/pull/741#issuecomment-1263569503  

An automated preview of the documentation is available at [https://741.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://741.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 26

> Username: cppalliance-bot  
> Created_at: 2022-09-30 13:41:40 UTC  
> Url: https://github.com/boostorg/json/pull/741#issuecomment-1263592631  

An automated preview of the documentation is available at [https://741.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://741.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 27

> Username: cppalliance-bot  
> Created_at: 2022-09-30 14:16:37 UTC  
> Url: https://github.com/boostorg/json/pull/741#issuecomment-1263634989  

An automated preview of the documentation is available at [https://741.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://741.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 28

> Username: cppalliance-bot  
> Created_at: 2022-09-30 14:41:38 UTC  
> Url: https://github.com/boostorg/json/pull/741#issuecomment-1263665256  

An automated preview of the documentation is available at [https://741.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://741.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 29

> Username: cppalliance-bot  
> Created_at: 2022-09-30 15:49:37 UTC  
> Url: https://github.com/boostorg/json/pull/741#issuecomment-1263740357  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/741/pullrequest-condensed-efbeb71.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/741/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/741/pullrequest.html)

---

## Comment 30

> Username: cppalliance-bot  
> Created_at: 2022-09-30 15:56:40 UTC  
> Url: https://github.com/boostorg/json/pull/741#issuecomment-1263747246  

An automated preview of the documentation is available at [https://741.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://741.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 31

> Username: cppalliance-bot  
> Created_at: 2022-09-30 17:04:36 UTC  
> Url: https://github.com/boostorg/json/pull/741#issuecomment-1263812284  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/741/pullrequest-condensed-07f1b61.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/741/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/741/pullrequest.html)

---

## Comment 32

> Username: cppalliance-bot  
> Created_at: 2022-10-05 06:41:28 UTC  
> Url: https://github.com/boostorg/json/pull/741#issuecomment-1268012351  

An automated preview of the documentation is available at [https://741.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://741.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 33

> Username: cppalliance-bot  
> Created_at: 2022-10-05 06:46:27 UTC  
> Url: https://github.com/boostorg/json/pull/741#issuecomment-1268017525  

An automated preview of the documentation is available at [https://741.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://741.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 34

> Username: cppalliance-bot  
> Created_at: 2022-10-05 07:01:28 UTC  
> Url: https://github.com/boostorg/json/pull/741#issuecomment-1268030454  

An automated preview of the documentation is available at [https://741.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://741.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 35

> Username: cppalliance-bot  
> Created_at: 2022-10-05 07:16:27 UTC  
> Url: https://github.com/boostorg/json/pull/741#issuecomment-1268044316  

An automated preview of the documentation is available at [https://741.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://741.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 36

> Username: cppalliance-bot  
> Created_at: 2022-10-05 08:19:37 UTC  
> Url: https://github.com/boostorg/json/pull/741#issuecomment-1268105710  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/741/pullrequest-condensed-fc81469.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/741/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/741/pullrequest.html)

---

## Comment 37

> Username: cppalliance-bot  
> Created_at: 2022-10-05 08:36:28 UTC  
> Url: https://github.com/boostorg/json/pull/741#issuecomment-1268125835  

An automated preview of the documentation is available at [https://741.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://741.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 38

> Username: cppalliance-bot  
> Created_at: 2022-10-05 09:44:32 UTC  
> Url: https://github.com/boostorg/json/pull/741#issuecomment-1268200847  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/741/pullrequest-condensed-81bb6e9.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/741/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/741/pullrequest.html)

---

## Comment 39

> Username: cppalliance-bot  
> Created_at: 2022-10-05 11:56:33 UTC  
> Url: https://github.com/boostorg/json/pull/741#issuecomment-1268338675  

An automated preview of the documentation is available at [https://741.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://741.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 40

> Username: cppalliance-bot  
> Created_at: 2022-10-05 13:04:36 UTC  
> Url: https://github.com/boostorg/json/pull/741#issuecomment-1268409052  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/741/pullrequest-condensed-e8c5fe9.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/741/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/741/pullrequest.html)

---

## Comment 41

> Username: cppalliance-bot  
> Created_at: 2022-10-05 13:56:37 UTC  
> Url: https://github.com/boostorg/json/pull/741#issuecomment-1268473841  

An automated preview of the documentation is available at [https://741.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://741.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 42

> Username: cppalliance-bot  
> Created_at: 2022-10-05 15:11:36 UTC  
> Url: https://github.com/boostorg/json/pull/741#issuecomment-1268579474  

An automated preview of the documentation is available at [https://741.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://741.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 43

> Username: cppalliance-bot  
> Created_at: 2022-10-05 16:14:36 UTC  
> Url: https://github.com/boostorg/json/pull/741#issuecomment-1268645813  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/741/pullrequest-condensed-02b3337.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/741/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/741/pullrequest.html)

---

## Comment 44

> Username: cppalliance-bot  
> Created_at: 2022-10-11 13:31:32 UTC  
> Url: https://github.com/boostorg/json/pull/741#issuecomment-1274691880  

An automated preview of the documentation is available at [https://741.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://741.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 45

> Username: cppalliance-bot  
> Created_at: 2022-10-11 14:39:41 UTC  
> Url: https://github.com/boostorg/json/pull/741#issuecomment-1274805124  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/741/pullrequest-condensed-af820d5.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/741/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/741/pullrequest.html)

---

## Comment 46

> Username: cppalliance-bot  
> Created_at: 2022-10-12 09:56:32 UTC  
> Url: https://github.com/boostorg/json/pull/741#issuecomment-1275904342  

An automated preview of the documentation is available at [https://741.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://741.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 47

> Username: cppalliance-bot  
> Created_at: 2022-10-12 10:59:39 UTC  
> Url: https://github.com/boostorg/json/pull/741#issuecomment-1275977656  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/741/pullrequest-condensed-b0cbbeb.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/741/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/741/pullrequest.html)

---

## Comment 48

> Username: cppalliance-bot  
> Created_at: 2022-10-12 18:35:09 UTC  
> Url: https://github.com/boostorg/json/pull/741#issuecomment-1276578432  

An automated preview of the documentation is available at [https://741.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://741.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 49

> Username: cppalliance-bot  
> Created_at: 2022-10-12 20:06:21 UTC  
> Url: https://github.com/boostorg/json/pull/741#issuecomment-1276676176  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/741/pullrequest-condensed-258f15c.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/741/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/741/pullrequest.html)

---

## Comment 50

> Username: cppalliance-bot  
> Created_at: 2022-10-13 04:46:29 UTC  
> Url: https://github.com/boostorg/json/pull/741#issuecomment-1277026767  

An automated preview of the documentation is available at [https://741.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://741.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 51

> Username: cppalliance-bot  
> Created_at: 2022-10-13 05:54:41 UTC  
> Url: https://github.com/boostorg/json/pull/741#issuecomment-1277066857  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/741/pullrequest-condensed-439a0a5.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/741/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/741/pullrequest.html)

---

## Review 52 [Commented]

> Username: pdimov  
> Created_at: 2022-10-13 14:12:47 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/741#pullrequestreview-1140877992  

📁 include/boost/json/conversion.hpp

```diff
  84 |+    namespace boost
  85 |+    {
  86 |+    namespace jsonj
```

> Username: pdimov  
> Created_at: 2022-10-13 14:12:47 UTC  
> Url: https://github.com/boostorg/json/pull/741#discussion_r994700654  

Typo? jsonj

> Username: grisumbras  
> Created_at: 2022-10-14 14:22:55 UTC  
> Url: https://github.com/boostorg/json/pull/741#discussion_r995818769  

Yes, it was a typo


---

## Review 53 [Commented]

> Username: pdimov  
> Created_at: 2022-10-13 14:15:39 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/741#pullrequestreview-1140883539  

📁 include/boost/json/system_error.hpp

```diff
  51 |+         // Return the value or throw an exception if has_value() == false
  52 |+         constexpr T& value();
  53 |+         constexpr T& operator*();
```

> Username: pdimov  
> Created_at: 2022-10-13 14:15:38 UTC  
> Updated_at: 2022-10-13 14:15:39 UTC  
> Url: https://github.com/boostorg/json/pull/741#discussion_r994704359  

`operator*` is not synonymous with `value`, it doesn't throw when `!has_value()`, but is UB.

> Username: grisumbras  
> Created_at: 2022-10-14 14:23:08 UTC  
> Url: https://github.com/boostorg/json/pull/741#discussion_r995818971  

Reorganised


---

## Comment 54

> Username: cppalliance-bot  
> Created_at: 2022-10-14 14:26:33 UTC  
> Url: https://github.com/boostorg/json/pull/741#issuecomment-1279084674  

An automated preview of the documentation is available at [https://741.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://741.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 55

> Username: cppalliance-bot  
> Created_at: 2022-10-14 14:31:34 UTC  
> Url: https://github.com/boostorg/json/pull/741#issuecomment-1279090589  

An automated preview of the documentation is available at [https://741.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html](https://741.jsondocs.prtest.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 56

> Username: cppalliance-bot  
> Created_at: 2022-10-14 15:34:44 UTC  
> Url: https://github.com/boostorg/json/pull/741#issuecomment-1279162431  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/741/pullrequest-condensed-ce8bb24.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/741/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/741/pullrequest.html)

---
