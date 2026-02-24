# #169 Refactoring: Simplify templates based on (supported) char types [Merged]

> Username: Flamefire  
> Created at: 2023-05-14 16:22:38 UTC  
> Updated at: 2023-05-15 07:45:38 UTC  
> Merged at: 2023-05-15 07:45:35 UTC  
> Closed at: 2023-05-15 07:45:36 UTC  
> Url: https://github.com/boostorg/locale/pull/169  

Instead of explicit instantiation for each supported char type and an extra copy for documentation generation reduce to only a single template and instantiate the whole class explicitly. To ensure a reasonable error is generated introduce an `is_supported_char`-trait and `static_assert` on it. To further reduce the repetition when instantiating the templates create a macro to loop over the supported char types.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2023-05-15 03:07:45 UTC  
> Updated_at: 2023-05-15 04:24:13 UTC  
> Url: https://github.com/boostorg/locale/pull/169#issuecomment-1547128733  

## [Codecov](https://app.codecov.io/gh/boostorg/locale/pull/169?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#169](https://app.codecov.io/gh/boostorg/locale/pull/169?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (9b0e615) into [develop](https://app.codecov.io/gh/boostorg/locale/commit/78d8a4e3283d64257f52b4f5bb4b98c9568a1e96?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (78d8a4e) will **decrease** coverage by `0.01%`.  
> The diff coverage is `100.00%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/locale/pull/169/graphs/tree.svg?width=650&height=150&src=pr&token=vF6mWvgGUn&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/locale/pull/169?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #169      +/-   ##  
===========================================  
- Coverage    91.47%   91.46%   -0.01%       
===========================================  
  Files          112      112                
  Lines         9804     9772      -32       
===========================================  
- Hits          8968     8938      -30       
+ Misses         836      834       -2       
```  
  
  
| [Impacted Files](https://app.codecov.io/gh/boostorg/locale/pull/169?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/locale/boundary/facets.hpp](https://app.codecov.io/gh/boostorg/locale/pull/169?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvYm91bmRhcnkvZmFjZXRzLmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/locale/collator.hpp](https://app.codecov.io/gh/boostorg/locale/pull/169?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvY29sbGF0b3IuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/locale/conversion.hpp](https://app.codecov.io/gh/boostorg/locale/pull/169?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvY29udmVyc2lvbi5ocHA=) | `100.00% <ø> (ø)` | |  
| [include/boost/locale/generic\_codecvt.hpp](https://app.codecov.io/gh/boostorg/locale/pull/169?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvZ2VuZXJpY19jb2RlY3Z0LmhwcA==) | `95.00% <ø> (ø)` | |  
| [include/boost/locale/gnu\_gettext.hpp](https://app.codecov.io/gh/boostorg/locale/pull/169?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvZ251X2dldHRleHQuaHBw) | `100.00% <ø> (ø)` | |  
| [include/boost/locale/info.hpp](https://app.codecov.io/gh/boostorg/locale/pull/169?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvaW5mby5ocHA=) | `100.00% <ø> (ø)` | |  
| [src/boost/locale/icu/boundary.cpp](https://app.codecov.io/gh/boostorg/locale/pull/169?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9pY3UvYm91bmRhcnkuY3Bw) | `88.11% <ø> (ø)` | |  
| [src/boost/locale/icu/formatter.cpp](https://app.codecov.io/gh/boostorg/locale/pull/169?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2Jvb3N0L2xvY2FsZS9pY3UvZm9ybWF0dGVyLmNwcA==) | `78.78% <ø> (ø)` | |  
| [include/boost/locale/date\_time\_facet.hpp](https://app.codecov.io/gh/boostorg/locale/pull/169?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvZGF0ZV90aW1lX2ZhY2V0LmhwcA==) | `100.00% <100.00%> (ø)` | |  
| [include/boost/locale/message.hpp](https://app.codecov.io/gh/boostorg/locale/pull/169?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9sb2NhbGUvbWVzc2FnZS5ocHA=) | `100.00% <100.00%> (ø)` | |  
| ... and [2 more](https://app.codecov.io/gh/boostorg/locale/pull/169?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/locale/pull/169?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/locale/pull/169?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [78d8a4e...9b0e615](https://app.codecov.io/gh/boostorg/locale/pull/169?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
