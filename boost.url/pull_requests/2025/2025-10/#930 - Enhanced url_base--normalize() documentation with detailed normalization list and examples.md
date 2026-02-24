# #930 Enhanced url_base::normalize() documentation with detailed normalization list and examples [Closed]

> Username: Copilot  
> Created at: 2025-10-13 17:33:52 UTC  
> Updated at: 2025-10-14 02:05:55 UTC  
> Closed at: 2025-10-13 20:02:16 UTC  
> Url: https://github.com/boostorg/url/pull/930  

## Overview  
  
This PR enhances the Javadoc documentation for `url_base::normalize()` to explicitly list each normalization it applies, along with before/after examples for each type. This addresses issue #XXX which requested more detailed exposition in the documentation.  
  
## What Changed  
  
The documentation comment for `url_base::normalize()` now includes:  
  
### Detailed Normalization Breakdown  
  
**Scheme Normalization**  
- Converts the scheme to lowercase  
- Example: `HTTP://www.example.com → http://www.example.com`  
  
**Authority Normalization**  
- Converts percent-encoding triplets to uppercase  
- Decodes percent-encoded octets of unreserved characters  
- Converts the host to lowercase  
- Examples:  
  - `http://user%2Aname@EXAMPLE.COM → http://user%2Aname@example.com`  
  - `http://user%7Ename@example.com → http://user~name@example.com`  
  
**Path Normalization**  
- Converts percent-encoding triplets to uppercase  
- Decodes percent-encoded octets of unreserved characters  
- Removes dot segments ("." and "..")  
- Examples:  
  - `http://example.com/a/b/../c → http://example.com/a/c`  
  - `http://example.com/%7Euser → http://example.com/~user`  
  - `http://example.com/a%2fb → http://example.com/a%2Fb`  
  
**Query Normalization**  
- Converts percent-encoding triplets to uppercase  
- Decodes percent-encoded octets of unreserved characters  
- Examples:  
  - `http://example.com/?key=%7Evalue → http://example.com/?key=~value`  
  - `http://example.com/?key=%2avalue → http://example.com/?key=%2Avalue`  
  
**Fragment Normalization**  
- Converts percent-encoding triplets to uppercase  
- Decodes percent-encoded octets of unreserved characters  
- Examples:  
  - `http://example.com/#%7Esection → http://example.com/#~section`  
  - `http://example.com/#%2asection → http://example.com/#%2Asection`  
  
**Unreserved Characters**  
- Documents which characters are considered unreserved: `A-Z a-z 0-9 - . _ ~`  
- These characters are decoded when percent-encoded during normalization  
  
## Scope  
  
This PR contains **only documentation changes** to the `url_base::normalize()` member function. No code, tests, or other documentation files were modified. The changes follow the existing Doxygen style used throughout the project and are based on the actual implementation behavior and RFC 3986 specifications.  
  
## Related  
  
Fixes #XXX (Javadoc for url_base::normalize needs more exposition)  
  
<!-- START COPILOT CODING AGENT SUFFIX -->  
  
  
  
<details>  
  
<summary>Original prompt</summary>  
  
>   
> ----  
>   
> *This section details on the original issue you should resolve*  
>   
> <issue_title>Javadoc for url_base::normalize needs more exposition</issue_title>  
> <issue_description>The Javadoc for `url_base::normalize` should explicitly list each of the normalization that it applies, along with examples of before and after for each. Any pull request should modify ONLY the documentation comment attached to the `url_base::normalize` member function.</issue_description>  
>   
> ## Comments on the Issue (you are @copilot in this section)  
>   
> <comments>  
> </comments>  
>   
  
  
</details>  
  
Fixes boostorg/url#929  
  
<!-- START COPILOT CODING AGENT TIPS -->  
---  
  
💬 Share your feedback on Copilot coding agent for the chance to win a $200 gift card! Click [here](https://survey3.medallia.com/?EAHeSx-AP01bZqG0Ld9QLQ) to start the survey.

---

## Comment 1

> Username: alandefreitas  
> Created_at: 2025-10-13 20:02:16 UTC  
> Url: https://github.com/boostorg/url/pull/930#issuecomment-3398894792  

Copilot is confidently making lots of mistakes here. I'll close this one so we don't get confused and remember to address the issue.

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2025-10-13 21:10:41 UTC  
> Updated_at: 2025-10-14 02:05:55 UTC  
> Url: https://github.com/boostorg/url/pull/930#issuecomment-3399082581  

## [Codecov](https://app.codecov.io/gh/boostorg/url/pull/930?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:white_check_mark: All modified and coverable lines are covered by tests.  
:white_check_mark: Project coverage is 99.22%. Comparing base ([`be06301`](https://app.codecov.io/gh/boostorg/url/commit/be06301557b1c83e3093166aa643d36dbb86a4f2?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`e101586`](https://app.codecov.io/gh/boostorg/url/commit/e1015867e4931d5b7c42073e903c59962a5fd6c9?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/url/pull/930/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/url/pull/930?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #930   +/-   ##  
========================================  
  Coverage    99.22%   99.22%             
========================================  
  Files          158      158             
  Lines         8598     8598             
========================================  
  Hits          8531     8531             
  Misses          67       67             
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/url/pull/930?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/url/url\_base.hpp](https://app.codecov.io/gh/boostorg/url/pull/930?src=pr&el=tree&filepath=include%2Fboost%2Furl%2Furl_base.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvdXJsX2Jhc2UuaHBw) | `100.00% <ø> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/url/pull/930?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/url/pull/930?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [be06301...e101586](https://app.codecov.io/gh/boostorg/url/pull/930?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---
