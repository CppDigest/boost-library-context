# #775 docs: fix reference issues [Merged]

> Username: alandefreitas  
> Created at: 2023-07-27 19:01:22 UTC  
> Updated at: 2024-02-21 00:55:55 UTC  
> Merged at: 2023-07-28 14:02:02 UTC  
> Closed at: 2023-07-28 14:02:02 UTC  
> Url: https://github.com/boostorg/url/pull/775  

This PR fixes issues that were causing warnings when generating the reference with docca. Along with other fixes recently pushed in `boostorg/docca`, it should completely fix all problems related to the reference.  
  
- 2e0c3eec) docs: update javadoc deprecated references: This commit fixes a mistake where the javadoc for many deprecated aliases included references to the deprecated alias `boost::core::string_view` instead of the correct deprecated aliases `boost::optional`, `system::error_category`, `system::error_code`, `system::error_condition`, `system::system_error`, and `system::result`.  
- 3ded066a) docs: remove references to variant: This commit removes links and references to 'variant' in both the .qbk and .xml files under the documentation directory. These references were removed as the `variant` alias has been deprecated in 96438f68.  
- 0717afec) docs: replace @ref prefix with backtick for references: This change alters the way we reference external symbols within the documentation comments. These direct references became invalid since 96438f68.  
- aba72cb5) docs: refactor variable name in url::set_params: Renamed method parameter `s` to `ps` in `url::set_params` to ensure it matches the documentation copied from `url_base::set_params`.

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2023-07-27 19:12:34 UTC  
> Url: https://github.com/boostorg/url/pull/775#issuecomment-1654324565  

An automated preview of the documentation is available at [https://775.url.prtest2.cppalliance.org/libs/url/doc/html/index.html](https://775.url.prtest2.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2023-07-27 19:22:49 UTC  
> Updated_at: 2023-07-27 20:37:47 UTC  
> Url: https://github.com/boostorg/url/pull/775#issuecomment-1654390453  

## [Codecov](https://app.codecov.io/gh/boostorg/url/pull/775?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#775](https://app.codecov.io/gh/boostorg/url/pull/775?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (aba72cb) into [develop](https://app.codecov.io/gh/boostorg/url/commit/516e0093c55271a6ec9b9f271292fc29bcd586cd?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (516e009) will **not change** coverage.  
> The diff coverage is `100.00%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/url/pull/775/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/url/pull/775?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #775   +/-   ##  
========================================  
  Coverage    97.27%   97.27%             
========================================  
  Files          156      156             
  Lines         8565     8565             
========================================  
  Hits          8332     8332             
  Misses         233      233             
```  
  
  
| [Files Changed](https://app.codecov.io/gh/boostorg/url/pull/775?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/url/authority\_view.hpp](https://app.codecov.io/gh/boostorg/url/pull/775?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvYXV0aG9yaXR5X3ZpZXcuaHBw) | `93.75% <ø> (ø)` | |  
| [include/boost/url/decode\_view.hpp](https://app.codecov.io/gh/boostorg/url/pull/775?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZGVjb2RlX3ZpZXcuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/url/grammar/delim\_rule.hpp](https://app.codecov.io/gh/boostorg/url/pull/775?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZ3JhbW1hci9kZWxpbV9ydWxlLmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/url/grammar/variant\_rule.hpp](https://app.codecov.io/gh/boostorg/url/pull/775?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvZ3JhbW1hci92YXJpYW50X3J1bGUuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/url/param.hpp](https://app.codecov.io/gh/boostorg/url/pull/775?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvcGFyYW0uaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/url/pct\_string\_view.hpp](https://app.codecov.io/gh/boostorg/url/pull/775?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvcGN0X3N0cmluZ192aWV3LmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/url/url\_base.hpp](https://app.codecov.io/gh/boostorg/url/pull/775?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvdXJsX2Jhc2UuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/url/url\_view.hpp](https://app.codecov.io/gh/boostorg/url/pull/775?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvdXJsX3ZpZXcuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/url/url.hpp](https://app.codecov.io/gh/boostorg/url/pull/775?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvdXJsLmhwcA==) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/url/pull/775?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/url/pull/775?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [516e009...aba72cb](https://app.codecov.io/gh/boostorg/url/pull/775?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2023-07-27 19:28:00 UTC  
> Url: https://github.com/boostorg/url/pull/775#issuecomment-1654407243  

GCOVR code coverage report [https://775.url.prtest2.cppalliance.org/gcovr/index.html](https://775.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://775.url.prtest2.cppalliance.org/genhtml/index.html](https://775.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://775.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://775.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 4

> Username: vinniefalco  
> Created_at: 2023-07-27 19:59:43 UTC  
> Url: https://github.com/boostorg/url/pull/775#issuecomment-1654472418  

lovely

---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2023-07-27 20:22:32 UTC  
> Url: https://github.com/boostorg/url/pull/775#issuecomment-1654516676  

An automated preview of the documentation is available at [https://775.url.prtest2.cppalliance.org/libs/url/doc/html/index.html](https://775.url.prtest2.cppalliance.org/libs/url/doc/html/index.html)

---

## Comment 6

> Username: cppalliance-bot  
> Created_at: 2023-07-27 20:47:07 UTC  
> Url: https://github.com/boostorg/url/pull/775#issuecomment-1654546330  

GCOVR code coverage report [https://775.url.prtest2.cppalliance.org/gcovr/index.html](https://775.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://775.url.prtest2.cppalliance.org/genhtml/index.html](https://775.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://775.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://775.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 7

> Username: alandefreitas  
> Created_at: 2023-07-27 21:03:03 UTC  
> Url: https://github.com/boostorg/url/pull/775#issuecomment-1654581979  

@sdarwin The runner selection seems to be getting stuck.

---

## Comment 8

> Username: sdarwin  
> Created_at: 2023-07-28 13:10:52 UTC  
> Url: https://github.com/boostorg/url/pull/775#issuecomment-1655661460  

> The runner selection seems to be getting stuck.  
  
@alandefreitas  The issue is probably `cancel-in-progress: true`.    
  
There are multiple levels of complexity with that setting for the self-hosted runners.  
  
1. A workflow is underway and all the jobs are mid-process and running. Then a cancellation happens. Jobs are cancelled, and new ones are started.  
2. A workflow was just start, and many servers are initiating. Those runners aren't yet reachable. Then a cancellation and restart occurs.  
3. Multiple PRs are running simultaneously. They all get cancelled/restarted. Some of them in-progress, others in an intermediate state where newly launched runners aren't online yet.  
  
It looks like yesterday at around the same time multiple PRs were restarted. I will investigate more, and open an issue with the developers. For now, the fix is to cancel all stuck workflows. Wait 5 or 10 minutes. Restart.  Allow them to run without interruption.

---

## Comment 9

> Username: sdarwin  
> Created_at: 2023-08-03 16:23:16 UTC  
> Url: https://github.com/boostorg/url/pull/775#issuecomment-1664278540  

Theoretically a fix is now in-place, see https://github.com/philips-labs/terraform-aws-github-runner , pull request: 3408. Let me know if similar issues still happen.

---
