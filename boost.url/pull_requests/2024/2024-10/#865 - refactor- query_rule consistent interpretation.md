# #865 refactor: query_rule consistent interpretation [Merged]

> Username: alandefreitas  
> Created at: 2024-10-02 14:54:06 UTC  
> Updated at: 2024-10-04 14:32:35 UTC  
> Merged at: 2024-10-04 14:32:08 UTC  
> Closed at: 2024-10-04 14:32:08 UTC  
> Url: https://github.com/boostorg/url/pull/865  

query_rule broke a grammar parse invariant by using different interpretations of the string depending on whether the reason for termination was the end of a string or a continuation with an invalid character.  
  
fix #864

---

## Review 1 [Commented]

> Username: vinniefalco  
> Created_at: 2024-10-02 14:58:01 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/865#pullrequestreview-2343219287  

📁 src/rfc/detail/query_part_rule.hpp

```diff
  53 |-         if(! rv)
  54 |-             return rv.error();
  53 |+         BOOST_ASSERT_MSG( rv, "query_rule is optionally empty and must not fail" );
```

> Username: vinniefalco  
> Created_at: 2024-10-02 14:58:00 UTC  
> Updated_at: 2024-10-02 14:58:01 UTC  
> Url: https://github.com/boostorg/url/pull/865#discussion_r1784706892  

when did we start putting strings in the asserts, instead of just leaving a comment before the line?


---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2024-10-02 14:59:49 UTC  
> Url: https://github.com/boostorg/url/pull/865#issuecomment-2388885202  

Antora version: an automated preview of the documentation is available at [https://865.urlantora.prtest2.cppalliance.org/site/index.html](https://865.urlantora.prtest2.cppalliance.org/site/index.html)

---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2024-10-02 15:18:49 UTC  
> Updated_at: 2024-10-04 14:32:35 UTC  
> Url: https://github.com/boostorg/url/pull/865#issuecomment-2388952672  

## [Codecov](https://app.codecov.io/gh/boostorg/url/pull/865?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 99.19%. Comparing base [(`78ed463`)](https://app.codecov.io/gh/boostorg/url/commit/78ed463014e161c7a980696803a4948a29375c6c?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`8038474`)](https://app.codecov.io/gh/boostorg/url/commit/80384743e8979c9f73911167d1e8333dedd0cef4?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 6 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/url/pull/865/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/url/pull/865?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #865      +/-   ##  
===========================================  
- Coverage    99.21%   99.19%   -0.03%       
===========================================  
  Files          157      157                
  Lines         8421     8415       -6       
===========================================  
- Hits          8355     8347       -8       
- Misses          66       68       +2       
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/url/pull/865?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/url/grammar/impl/variant\_rule.hpp](https://app.codecov.io/gh/boostorg/url/pull/865?src=pr&el=tree&filepath=include%2Fboost%2Furl%2Fgrammar%2Fimpl%2Fvariant_rule.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZ3JhbW1hci9pbXBsL3ZhcmlhbnRfcnVsZS5ocHA=) | `100.00% <ø> (ø)` | |  
| [src/rfc/absolute\_uri\_rule.cpp](https://app.codecov.io/gh/boostorg/url/pull/865?src=pr&el=tree&filepath=src%2Frfc%2Fabsolute_uri_rule.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL3JmYy9hYnNvbHV0ZV91cmlfcnVsZS5jcHA=) | `96.42% <100.00%> (-3.58%)` | :arrow_down: |  
| [src/rfc/detail/query\_part\_rule.hpp](https://app.codecov.io/gh/boostorg/url/pull/865?src=pr&el=tree&filepath=src%2Frfc%2Fdetail%2Fquery_part_rule.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL3JmYy9kZXRhaWwvcXVlcnlfcGFydF9ydWxlLmhwcA==) | `100.00% <100.00%> (ø)` | |  
| [src/rfc/origin\_form\_rule.cpp](https://app.codecov.io/gh/boostorg/url/pull/865?src=pr&el=tree&filepath=src%2Frfc%2Forigin_form_rule.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL3JmYy9vcmlnaW5fZm9ybV9ydWxlLmNwcA==) | `94.73% <100.00%> (-0.27%)` | :arrow_down: |  
| [src/rfc/query\_rule.cpp](https://app.codecov.io/gh/boostorg/url/pull/865?src=pr&el=tree&filepath=src%2Frfc%2Fquery_rule.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL3JmYy9xdWVyeV9ydWxlLmNwcA==) | `100.00% <100.00%> (ø)` | |  
| [src/rfc/relative\_ref\_rule.cpp](https://app.codecov.io/gh/boostorg/url/pull/865?src=pr&el=tree&filepath=src%2Frfc%2Frelative_ref_rule.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL3JmYy9yZWxhdGl2ZV9yZWZfcnVsZS5jcHA=) | `95.65% <100.00%> (-0.35%)` | :arrow_down: |  
| [src/rfc/uri\_rule.cpp](https://app.codecov.io/gh/boostorg/url/pull/865?src=pr&el=tree&filepath=src%2Frfc%2Furi_rule.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL3JmYy91cmlfcnVsZS5jcHA=) | `96.96% <100.00%> (-3.04%)` | :arrow_down: |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/url/pull/865?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/url/pull/865?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [78ed463...8038474](https://app.codecov.io/gh/boostorg/url/pull/865?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2024-10-02 15:24:26 UTC  
> Url: https://github.com/boostorg/url/pull/865#issuecomment-2388965466  

GCOVR code coverage report [https://865.url.prtest2.cppalliance.org/gcovr/index.html](https://865.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://865.url.prtest2.cppalliance.org/genhtml/index.html](https://865.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://865.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://865.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2024-10-02 17:09:50 UTC  
> Url: https://github.com/boostorg/url/pull/865#issuecomment-2389187424  

Antora version: an automated preview of the documentation is available at [https://865.urlantora.prtest2.cppalliance.org/site/index.html](https://865.urlantora.prtest2.cppalliance.org/site/index.html)

---

## Comment 6

> Username: cppalliance-bot  
> Created_at: 2024-10-02 17:28:37 UTC  
> Url: https://github.com/boostorg/url/pull/865#issuecomment-2389225962  

Antora version: an automated preview of the documentation is available at [https://865.urlantora.prtest2.cppalliance.org/site/index.html](https://865.urlantora.prtest2.cppalliance.org/site/index.html)

---

## Comment 7

> Username: cppalliance-bot  
> Created_at: 2024-10-02 17:31:51 UTC  
> Url: https://github.com/boostorg/url/pull/865#issuecomment-2389231601  

GCOVR code coverage report [https://865.url.prtest2.cppalliance.org/gcovr/index.html](https://865.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://865.url.prtest2.cppalliance.org/genhtml/index.html](https://865.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://865.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://865.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 8

> Username: cppalliance-bot  
> Created_at: 2024-10-02 17:48:55 UTC  
> Url: https://github.com/boostorg/url/pull/865#issuecomment-2389260654  

GCOVR code coverage report [https://865.url.prtest2.cppalliance.org/gcovr/index.html](https://865.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://865.url.prtest2.cppalliance.org/genhtml/index.html](https://865.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://865.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://865.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 9

> Username: cppalliance-bot  
> Created_at: 2024-10-02 18:48:38 UTC  
> Url: https://github.com/boostorg/url/pull/865#issuecomment-2389454862  

Antora version: an automated preview of the documentation is available at [https://865.urlantora.prtest2.cppalliance.org/site/index.html](https://865.urlantora.prtest2.cppalliance.org/site/index.html)

---

## Comment 10

> Username: cppalliance-bot  
> Created_at: 2024-10-02 19:07:57 UTC  
> Url: https://github.com/boostorg/url/pull/865#issuecomment-2389486438  

GCOVR code coverage report [https://865.url.prtest2.cppalliance.org/gcovr/index.html](https://865.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://865.url.prtest2.cppalliance.org/genhtml/index.html](https://865.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://865.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://865.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 11

> Username: cppalliance-bot  
> Created_at: 2024-10-02 23:48:37 UTC  
> Url: https://github.com/boostorg/url/pull/865#issuecomment-2389991010  

Antora version: an automated preview of the documentation is available at [https://865.urlantora.prtest2.cppalliance.org/site/index.html](https://865.urlantora.prtest2.cppalliance.org/site/index.html)

---

## Comment 12

> Username: cppalliance-bot  
> Created_at: 2024-10-03 00:14:35 UTC  
> Url: https://github.com/boostorg/url/pull/865#issuecomment-2390203857  

GCOVR code coverage report [https://865.url.prtest2.cppalliance.org/gcovr/index.html](https://865.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://865.url.prtest2.cppalliance.org/genhtml/index.html](https://865.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://865.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://865.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 13

> Username: cppalliance-bot  
> Created_at: 2024-10-03 14:29:50 UTC  
> Url: https://github.com/boostorg/url/pull/865#issuecomment-2391573085  

Antora version: an automated preview of the documentation is available at [https://865.urlantora.prtest2.cppalliance.org/site/index.html](https://865.urlantora.prtest2.cppalliance.org/site/index.html)

---

## Comment 14

> Username: cppalliance-bot  
> Created_at: 2024-10-03 14:48:51 UTC  
> Url: https://github.com/boostorg/url/pull/865#issuecomment-2391620182  

GCOVR code coverage report [https://865.url.prtest2.cppalliance.org/gcovr/index.html](https://865.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://865.url.prtest2.cppalliance.org/genhtml/index.html](https://865.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://865.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://865.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---

## Review 15 [Commented]

> Username: vinniefalco  
> Created_at: 2024-10-03 15:18:03 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/865#pullrequestreview-2345932610  

📁 test/unit/params_ref.cpp

```diff
 409 |+                 BOOST_TEST((*it).value == "value2");
 410 |+                 ++it;
 411 |+                 BOOST_TEST(it == params.end());
```

> Username: vinniefalco  
> Created_at: 2024-10-03 15:18:02 UTC  
> Updated_at: 2024-10-03 15:18:03 UTC  
> Url: https://github.com/boostorg/url/pull/865#discussion_r1786421246  

It would be cool if lines 398 through 411 could be reduced to this one line:  
```  
check_eq( u.params(), {  
  { "key1", true, "value1" },  
  { "key2", true, "value2" } } );  
```  
  
Maybe in a separate PR? This would simplify a ton of test code...

> Username: alandefreitas  
> Created_at: 2024-10-03 16:31:18 UTC  
> Url: https://github.com/boostorg/url/pull/865#discussion_r1786520694  

I think I defined something like std::ranges::equal in one of these tests. Let me check. We could move it to some test_utils.hpp or something.


---

## Comment 16

> Username: cppalliance-bot  
> Created_at: 2024-10-03 19:14:45 UTC  
> Url: https://github.com/boostorg/url/pull/865#issuecomment-2392145098  

Antora version: an automated preview of the documentation is available at [https://865.urlantora.prtest2.cppalliance.org/site/index.html](https://865.urlantora.prtest2.cppalliance.org/site/index.html)

---

## Comment 17

> Username: cppalliance-bot  
> Created_at: 2024-10-03 19:18:33 UTC  
> Url: https://github.com/boostorg/url/pull/865#issuecomment-2392152457  

Antora version: an automated preview of the documentation is available at [https://865.urlantora.prtest2.cppalliance.org/site/index.html](https://865.urlantora.prtest2.cppalliance.org/site/index.html)

---

## Comment 18

> Username: cppalliance-bot  
> Created_at: 2024-10-03 19:28:37 UTC  
> Url: https://github.com/boostorg/url/pull/865#issuecomment-2392172387  

Antora version: an automated preview of the documentation is available at [https://865.urlantora.prtest2.cppalliance.org/site/index.html](https://865.urlantora.prtest2.cppalliance.org/site/index.html)

---

## Comment 19

> Username: cppalliance-bot  
> Created_at: 2024-10-03 19:40:10 UTC  
> Url: https://github.com/boostorg/url/pull/865#issuecomment-2392191743  

GCOVR code coverage report [https://865.url.prtest2.cppalliance.org/gcovr/index.html](https://865.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://865.url.prtest2.cppalliance.org/genhtml/index.html](https://865.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://865.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://865.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 20

> Username: cppalliance-bot  
> Created_at: 2024-10-03 20:02:54 UTC  
> Url: https://github.com/boostorg/url/pull/865#issuecomment-2392230143  

GCOVR code coverage report [https://865.url.prtest2.cppalliance.org/gcovr/index.html](https://865.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://865.url.prtest2.cppalliance.org/genhtml/index.html](https://865.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://865.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://865.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---
