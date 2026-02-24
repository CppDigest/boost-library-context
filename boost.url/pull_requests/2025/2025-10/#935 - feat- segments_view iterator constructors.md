# #935 feat: segments_view iterator constructors [Merged]

> Username: alandefreitas  
> Created at: 2025-10-22 00:09:20 UTC  
> Updated at: 2025-10-22 21:39:02 UTC  
> Merged at: 2025-10-22 16:05:46 UTC  
> Closed at: 2025-10-22 16:05:46 UTC  
> Url: https://github.com/boostorg/url/pull/935  

The relevant changes are: https://github.com/boostorg/url/pull/935/commits/8f4674a3d30c9aa809de8c37ca14f9f7f589f82b  
  
Other commits are related to the build errors that were blocking the CI.

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2025-10-22 00:12:58 UTC  
> Updated_at: 2025-10-22 14:54:05 UTC  
> Url: https://github.com/boostorg/url/pull/935#issuecomment-3429989101  

An automated preview of the documentation is available at [https://935.url.prtest2.cppalliance.org/index.html](https://935.url.prtest2.cppalliance.org/index.html)  
  
If more commits are pushed to the pull request, the docs will rebuild at the same URL.  
  
2025-10-22 14:54:03 UTC

---

## Comment 2

> Username: sdarwin  
> Created_at: 2025-10-22 00:59:24 UTC  
> Url: https://github.com/boostorg/url/pull/935#issuecomment-3430069090  

```  
libs/url/test/unit/grammar/grammar_parse.cpp: In member function ‘void boost::urls::grammar::parse_test::testRef()’:  
libs/url/test/unit/grammar/grammar_parse.cpp:27:9: error: ‘BOOST_STATIC_ASSERT’ was not declared in this scope; did you mean ‘BOOST_HAS_STATIC_ASSERT’?  
   27 |         BOOST_STATIC_ASSERT(is_rule<  
      |         ^~~~~~~~~~~~~~~~~~~  
      |         BOOST_HAS_STATIC_ASSERT  
 ```

---

## Comment 3

> Username: alandefreitas  
> Created_at: 2025-10-22 01:47:54 UTC  
> Updated_at: 2025-10-22 01:49:14 UTC  
> Url: https://github.com/boostorg/url/pull/935#issuecomment-3430166161  

@sdarwin Where's that from? That's been fixed, right?

---

## Comment 4

> Username: codecov[bot]  
> Created_at: 2025-10-22 04:39:03 UTC  
> Updated_at: 2025-10-22 21:39:02 UTC  
> Url: https://github.com/boostorg/url/pull/935#issuecomment-3430468428  

## [Codecov](https://app.codecov.io/gh/boostorg/url/pull/935?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:white_check_mark: All modified and coverable lines are covered by tests.  
:white_check_mark: Project coverage is 99.22%. Comparing base ([`e1c9031`](https://app.codecov.io/gh/boostorg/url/commit/e1c903136100e0baa025abad2a48336ba03869f4?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`d4884a6`](https://app.codecov.io/gh/boostorg/url/commit/d4884a6cdec3402e152324428a0b26042ecf56a4?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
:warning: Report is 4 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/url/pull/935/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/url/pull/935?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #935   +/-   ##  
========================================  
  Coverage    99.22%   99.22%             
========================================  
  Files          158      159    +1       
  Lines         8604     8657   +53       
========================================  
+ Hits          8537     8590   +53       
  Misses          67       67             
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/url/pull/935?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [example/router/impl/router.hpp](https://app.codecov.io/gh/boostorg/url/pull/935?src=pr&el=tree&filepath=example%2Frouter%2Fimpl%2Frouter.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-ZXhhbXBsZS9yb3V0ZXIvaW1wbC9yb3V0ZXIuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/url/detail/any\_params\_iter.hpp](https://app.codecov.io/gh/boostorg/url/pull/935?src=pr&el=tree&filepath=include%2Fboost%2Furl%2Fdetail%2Fany_params_iter.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZGV0YWlsL2FueV9wYXJhbXNfaXRlci5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/url/detail/any\_segments\_iter.hpp](https://app.codecov.io/gh/boostorg/url/pull/935?src=pr&el=tree&filepath=include%2Fboost%2Furl%2Fdetail%2Fany_segments_iter.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZGV0YWlsL2FueV9zZWdtZW50c19pdGVyLmhwcA==) | `98.50% <ø> (ø)` | |  
| [include/boost/url/detail/segments\_range.hpp](https://app.codecov.io/gh/boostorg/url/pull/935?src=pr&el=tree&filepath=include%2Fboost%2Furl%2Fdetail%2Fsegments_range.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZGV0YWlsL3NlZ21lbnRzX3JhbmdlLmhwcA==) | `100.00% <100.00%> (ø)` | |  
| [include/boost/url/grammar/charset.hpp](https://app.codecov.io/gh/boostorg/url/pull/935?src=pr&el=tree&filepath=include%2Fboost%2Furl%2Fgrammar%2Fcharset.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZ3JhbW1hci9jaGFyc2V0LmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/url/grammar/impl/range\_rule.hpp](https://app.codecov.io/gh/boostorg/url/pull/935?src=pr&el=tree&filepath=include%2Fboost%2Furl%2Fgrammar%2Fimpl%2Frange_rule.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZ3JhbW1hci9pbXBsL3JhbmdlX3J1bGUuaHBw) | `95.47% <ø> (ø)` | |  
| [include/boost/url/grammar/impl/variant\_rule.hpp](https://app.codecov.io/gh/boostorg/url/pull/935?src=pr&el=tree&filepath=include%2Fboost%2Furl%2Fgrammar%2Fimpl%2Fvariant_rule.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZ3JhbW1hci9pbXBsL3ZhcmlhbnRfcnVsZS5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/url/grammar/optional\_rule.hpp](https://app.codecov.io/gh/boostorg/url/pull/935?src=pr&el=tree&filepath=include%2Fboost%2Furl%2Fgrammar%2Foptional_rule.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZ3JhbW1hci9vcHRpb25hbF9ydWxlLmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/url/grammar/range\_rule.hpp](https://app.codecov.io/gh/boostorg/url/pull/935?src=pr&el=tree&filepath=include%2Fboost%2Furl%2Fgrammar%2Frange_rule.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZ3JhbW1hci9yYW5nZV9ydWxlLmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/url/grammar/tuple\_rule.hpp](https://app.codecov.io/gh/boostorg/url/pull/935?src=pr&el=tree&filepath=include%2Fboost%2Furl%2Fgrammar%2Ftuple_rule.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZ3JhbW1hci90dXBsZV9ydWxlLmhwcA==) | `100.00% <ø> (ø)` | |  
| ... and [8 more](https://app.codecov.io/gh/boostorg/url/pull/935?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/url/pull/935?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/url/pull/935?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [e1c9031...d4884a6](https://app.codecov.io/gh/boostorg/url/pull/935?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---
