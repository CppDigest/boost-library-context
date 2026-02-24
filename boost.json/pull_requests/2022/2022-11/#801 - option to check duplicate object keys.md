# #801 option to check duplicate object keys [Open]

> Username: grisumbras  
> Created at: 2022-11-06 10:50:41 UTC  
> Updated at: 2025-10-23 12:05:00 UTC  
> Url: https://github.com/boostorg/json/pull/801  

Fix #127

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2022-11-06 12:49:33 UTC  
> Url: https://github.com/boostorg/json/pull/801#issuecomment-1304794500  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/801/pullrequest-condensed-3e3baed.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/801/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/801/pullrequest.html)

---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2022-11-06 12:59:40 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/801#pullrequestreview-1169593042  

📁 include/boost/json/detail/impl/handler.ipp

```diff
  56 |+     if( !ignore_duplicate_keys )
  57 |+         ec = st.check_duplicates(n);
  58 |+     if( ec.failed() )
```

> Username: vinniefalco  
> Created_at: 2022-11-06 12:59:39 UTC  
> Updated_at: 2022-11-06 12:59:40 UTC  
> Url: https://github.com/boostorg/json/pull/801#discussion_r1014825731  

you check `ec` even if `ignore_duplicate_keys` is true?

> Username: grisumbras  
> Created_at: 2022-11-06 13:00:53 UTC  
> Url: https://github.com/boostorg/json/pull/801#discussion_r1014825877  

Yeah, it's unnecessary


---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2022-11-07 14:30:29 UTC  
> Updated_at: 2025-08-02 08:37:28 UTC  
> Url: https://github.com/boostorg/json/pull/801#issuecomment-1305700832  

## [Codecov](https://app.codecov.io/gh/boostorg/json/pull/801?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:white_check_mark: All modified and coverable lines are covered by tests.  
:white_check_mark: Project coverage is 99.00%. Comparing base ([`4d22409`](https://app.codecov.io/gh/boostorg/json/commit/4d224096cea90bc8f0ad8182ac50958a78bc435c?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`8715073`](https://app.codecov.io/gh/boostorg/json/commit/8715073261ece5d38337032eb7561713d03c3e10?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
:warning: Report is 274 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/json/pull/801/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/json/pull/801?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #801   +/-   ##  
========================================  
  Coverage    99.00%   99.00%             
========================================  
  Files           71       71             
  Lines         6831     6833    +2       
========================================  
+ Hits          6763     6765    +2       
  Misses          68       68             
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/json/pull/801?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/detail/impl/handler.ipp](https://app.codecov.io/gh/boostorg/json/pull/801?src=pr&el=tree&filepath=include%2Fboost%2Fjson%2Fdetail%2Fimpl%2Fhandler.ipp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9pbXBsL2hhbmRsZXIuaXBw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/detail/object.hpp](https://app.codecov.io/gh/boostorg/json/pull/801?src=pr&el=tree&filepath=include%2Fboost%2Fjson%2Fdetail%2Fobject.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2RldGFpbC9vYmplY3QuaHBw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/impl/error.ipp](https://app.codecov.io/gh/boostorg/json/pull/801?src=pr&el=tree&filepath=include%2Fboost%2Fjson%2Fimpl%2Ferror.ipp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvZXJyb3IuaXBw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/impl/object.hpp](https://app.codecov.io/gh/boostorg/json/pull/801?src=pr&el=tree&filepath=include%2Fboost%2Fjson%2Fimpl%2Fobject.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvb2JqZWN0LmhwcA==) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/impl/object.ipp](https://app.codecov.io/gh/boostorg/json/pull/801?src=pr&el=tree&filepath=include%2Fboost%2Fjson%2Fimpl%2Fobject.ipp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvb2JqZWN0LmlwcA==) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/impl/parser.ipp](https://app.codecov.io/gh/boostorg/json/pull/801?src=pr&el=tree&filepath=include%2Fboost%2Fjson%2Fimpl%2Fparser.ipp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvcGFyc2VyLmlwcA==) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/impl/stream\_parser.ipp](https://app.codecov.io/gh/boostorg/json/pull/801?src=pr&el=tree&filepath=include%2Fboost%2Fjson%2Fimpl%2Fstream_parser.ipp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvc3RyZWFtX3BhcnNlci5pcHA=) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/impl/value\_stack.ipp](https://app.codecov.io/gh/boostorg/json/pull/801?src=pr&el=tree&filepath=include%2Fboost%2Fjson%2Fimpl%2Fvalue_stack.ipp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvdmFsdWVfc3RhY2suaXBw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/object.hpp](https://app.codecov.io/gh/boostorg/json/pull/801?src=pr&el=tree&filepath=include%2Fboost%2Fjson%2Fobject.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL29iamVjdC5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/json/value.hpp](https://app.codecov.io/gh/boostorg/json/pull/801?src=pr&el=tree&filepath=include%2Fboost%2Fjson%2Fvalue.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9qc29uL3ZhbHVlLmhwcA==) | `98.94% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/json/pull/801?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/json/pull/801?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [4d22409...8715073](https://app.codecov.io/gh/boostorg/json/pull/801?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---

## Comment 4

> Username: vinniefalco  
> Created_at: 2022-11-07 15:03:45 UTC  
> Url: https://github.com/boostorg/json/pull/801#issuecomment-1305745142  

yeah please don't merge this with the commit message as "different approach" :)

---

## Review 5 [Commented]

> Username: vinniefalco  
> Created_at: 2022-11-07 15:05:56 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/801#pullrequestreview-1170567560  

📁 include/boost/json/impl/object.ipp

```diff
 231 |             }
 232 |             // handle duplicate
 233 |+             if( !uo.ignore_duplicate_keys() )
```

> Username: vinniefalco  
> Created_at: 2022-11-07 15:05:55 UTC  
> Updated_at: 2022-11-07 15:05:56 UTC  
> Url: https://github.com/boostorg/json/pull/801#discussion_r1015535535  

This is a constant in the loop, so you should lift the conditional out of the loop


---

## Review 6 [Commented]

> Username: vinniefalco  
> Created_at: 2022-11-07 15:06:25 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/801#pullrequestreview-1170568426  

📁 include/boost/json/impl/object.ipp

```diff
 275 | 
 276 |             // handle duplicate
 277 |+             if( !uo.ignore_duplicate_keys() )
```

> Username: vinniefalco  
> Created_at: 2022-11-07 15:06:25 UTC  
> Url: https://github.com/boostorg/json/pull/801#discussion_r1015536129  

This is a constant in the loop, so you should lift the conditional out of the loop


---

## Comment 7

> Username: cppalliance-bot  
> Created_at: 2022-11-07 15:24:36 UTC  
> Url: https://github.com/boostorg/json/pull/801#issuecomment-1305775436  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/801/pullrequest-condensed-4cd5449.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/801/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/801/pullrequest.html)

---

## Review 8 [Commented]

> Username: vinniefalco  
> Created_at: 2022-11-07 16:58:50 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/801#pullrequestreview-1170763724  

📁 include/boost/json/value_stack.hpp

```diff
 359 |-     push_object(std::size_t n);
 359 |+     error_code
 360 |+     push_object(std::size_t n, bool ignore_duplicates = true);
```

> Username: vinniefalco  
> Created_at: 2022-11-07 16:58:50 UTC  
> Url: https://github.com/boostorg/json/pull/801#discussion_r1015671264  

I think I prefer it as `allow_duplicates`, defaulting to `false`. Rationale: The default settings should have the all-zero bit-pattern. It is also easier to understand this way, you are opting in to the setting by assigning `true`. Rather than opting out to disallowing duplicates by assigning `false`.

> Username: grisumbras  
> Created_at: 2022-11-12 12:56:43 UTC  
> Url: https://github.com/boostorg/json/pull/801#discussion_r1020758003  

"Ignore duplicates" and "allow duplicates" are the same thing (at least in this case). The reverse of "ingore" would be "forbid". So, "forbid_duplicate_keys" option, with `false` as default?


---

## Review 9 [Commented]

> Username: vinniefalco  
> Created_at: 2022-11-08 16:29:41 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/801#pullrequestreview-1172464919  

📁 include/boost/json/detail/value.hpp

```diff
 290 |+     static constexpr
 291 |+     index_t
 292 |+     null_index(Object const&) noexcept
```

> Username: vinniefalco  
> Created_at: 2022-11-08 16:29:41 UTC  
> Updated_at: 2022-11-08 16:29:55 UTC  
> Url: https://github.com/boostorg/json/pull/801#discussion_r1016861305  

Why are we mucking about with `value`? And why are these templates?


---

## Review 10 [Commented]

> Username: vinniefalco  
> Created_at: 2022-11-08 16:36:13 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/801#pullrequestreview-1172475928  

📁 include/boost/json/impl/object.hpp

```diff
 526 |-     while(size_--)
 523 |+ 
 524 |+     for( value* p = data_; p != end_; p += 2 )
```

> Username: vinniefalco  
> Created_at: 2022-11-08 16:36:13 UTC  
> Updated_at: 2022-11-08 16:36:14 UTC  
> Url: https://github.com/boostorg/json/pull/801#discussion_r1016868868  

Why did you change this loop? I prefer the `while` form, as it puts each of the statements on their own line. The `for` is wider, and puts multiple statements on one line, which makes debugging and code coverage worse.

> Username: vinniefalco  
> Created_at: 2022-11-12 20:47:16 UTC  
> Url: https://github.com/boostorg/json/pull/801#discussion_r1020804605  

I see why, because the loop is simpler.


---

## Comment 11

> Username: cppalliance-bot  
> Created_at: 2022-11-08 16:39:39 UTC  
> Url: https://github.com/boostorg/json/pull/801#issuecomment-1307506199  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/801/pullrequest-condensed-c688a11.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/801/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/801/pullrequest.html)

---

## Review 12 [Commented]

> Username: vinniefalco  
> Created_at: 2022-11-08 16:49:27 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/801#pullrequestreview-1172497997  

📁 include/boost/json/impl/object.hpp

```diff
 544 |+ std::size_t
 545 |+ unchecked_object::
 546 |+ size() const noexcept
```

> Username: vinniefalco  
> Created_at: 2022-11-08 16:49:26 UTC  
> Updated_at: 2022-11-08 16:49:27 UTC  
> Url: https://github.com/boostorg/json/pull/801#discussion_r1016883652  

Did we move this from a header that wasn't visible to a header that is visible?


---

## Comment 13

> Username: cppalliance-bot  
> Created_at: 2022-11-09 08:54:40 UTC  
> Url: https://github.com/boostorg/json/pull/801#issuecomment-1308421413  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/801/pullrequest-condensed-a7f6bd3.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/801/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/801/pullrequest.html)

---

## Review 14 [Commented]

> Username: vinniefalco  
> Created_at: 2022-11-12 20:48:03 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/801#pullrequestreview-1178161713  

📁 include/boost/json/detail/object.hpp

```diff
  76 |     }
  77 |+ 
  78 |+     inline
```

> Username: vinniefalco  
> Created_at: 2022-11-12 20:48:03 UTC  
> Url: https://github.com/boostorg/json/pull/801#discussion_r1020804687  

the `inline` goes on the definition not the declaration (my fault)


---

## Review 15 [Commented]

> Username: vinniefalco  
> Created_at: 2022-11-12 20:49:17 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/801#pullrequestreview-1178161789  

📁 include/boost/json/impl/object.ipp

```diff
  72 | 
  73 |+ template< bool SmallTable, bool IgnoreDuplicates >
  74 |+ void init_from_unchecked( object& obj, unchecked_object& uo )
```

> Username: vinniefalco  
> Created_at: 2022-11-12 20:49:17 UTC  
> Url: https://github.com/boostorg/json/pull/801#discussion_r1020804808  

Should this be `noexcept`?


---

## Review 16 [Commented]

> Username: vinniefalco  
> Created_at: 2022-11-12 20:50:12 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/801#pullrequestreview-1178161856  

📁 include/boost/json/impl/object.ipp

```diff
  75 |+ {
  76 |+     // insert all elements, keeping
  77 |+     // the last of any duplicate keys, unless IgnoreDuplicates is false.
```

> Username: vinniefalco  
> Created_at: 2022-11-12 20:50:12 UTC  
> Url: https://github.com/boostorg/json/pull/801#discussion_r1020804907  

There should be an assert here, the precondition of the function requires `capacity() >= uo.size()`


---

## Review 17 [Commented]

> Username: vinniefalco  
> Created_at: 2022-11-12 20:53:00 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/801#pullrequestreview-1178162066  

📁 include/boost/json/impl/value_stack.ipp

```diff
 366 | 
 367 |- void
 367 |+ error_code
```

> Username: vinniefalco  
> Created_at: 2022-11-12 20:52:59 UTC  
> Updated_at: 2022-11-12 20:53:00 UTC  
> Url: https://github.com/boostorg/json/pull/801#discussion_r1020805170  

this is going to force the construction of a new error code for every object even when there is no error and even when ignore_duplicates is true. It should be an outparam. Or even a bool


---

## Review 18 [Commented]

> Username: vinniefalco  
> Created_at: 2022-11-12 20:54:15 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/801#pullrequestreview-1178162163  

📁 include/boost/json/impl/value_stack.ipp

```diff
 382 |+     {
 383 |+         BOOST_JSON_FAIL( ec, error::duplicate_key );
 384 |+     }
```

> Username: vinniefalco  
> Created_at: 2022-11-12 20:54:14 UTC  
> Updated_at: 2022-11-12 20:54:15 UTC  
> Url: https://github.com/boostorg/json/pull/801#discussion_r1020805292  

I believe that the way this is implemented has impacted performance, which is causing the regression in the per-commit performance checker.


---

## Comment 19

> Username: czheng-bt  
> Created_at: 2025-10-22 19:30:22 UTC  
> Updated_at: 2025-10-22 19:30:45 UTC  
> Url: https://github.com/boostorg/json/pull/801#issuecomment-3433921415  

Hi, is there any updates to this? It's been a while since the last update. Will this feature get pushed forward? thanks!

---

## Comment 20

> Username: grisumbras  
> Created_at: 2025-10-23 12:05:00 UTC  
> Url: https://github.com/boostorg/json/pull/801#issuecomment-3436572713  

I am not very enthusiastic about this design. Normally parse_options affect the behabiour of the parser algorithm. But our parser cannot really deal with duplicate keys (it just forwards incoming syntatically valid data to the handler). So, the handler is what has to check for duplicates. Thus, this PR couples parse_options and the default handler, which smells of bad design.  
  
Also, if someone uses a parser customised with a different handler, then the option would have no effect. It seems like the correct move here is to give parser and stream_parser a member to make duplicates an error. But then this excludes `parse` and `operator>>` from this feature.

---
