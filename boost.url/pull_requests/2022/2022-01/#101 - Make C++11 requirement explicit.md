# #101 Make C++11 requirement explicit [Closed]

> Username: alandefreitas  
> Created at: 2022-01-10 18:48:43 UTC  
> Updated at: 2022-01-19 18:38:07 UTC  
> Closed at: 2022-01-12 20:06:32 UTC  
> Url: https://github.com/boostorg/url/pull/101  

This PR makes `cxx_std_11` an explicit requirement at configure stage. If the compiler doesn't support C++11 or  CMAKE_CXX_STANDARD is set to a lower version, the user will be warned about it in the configure step instead of allowing the compiler to try to compile the code and fail. This also improves possible error messages in CI.

---

## Comment 1

> Username: vinniefalco  
> Created_at: 2022-01-10 18:49:01 UTC  
> Url: https://github.com/boostorg/url/pull/101#issuecomment-1009239862  

@grisumbras @pdimov thoughts?

---

## Comment 2

> Username: pdimov  
> Created_at: 2022-01-10 18:56:39 UTC  
> Url: https://github.com/boostorg/url/pull/101#issuecomment-1009245488  

The `cxx_constexpr` requirement already does this.

---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2022-01-11 02:01:03 UTC  
> Url: https://github.com/boostorg/url/pull/101#issuecomment-1009531067  

# [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/101?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) Report  
> Merging [#101](https://codecov.io/gh/CPPAlliance/url/pull/101?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (73a3f93) into [develop](https://codecov.io/gh/CPPAlliance/url/commit/c62c4ed3d0f09cd87693f124829893e16fd3360f?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) (c62c4ed) will **increase** coverage by `0.02%`.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/CPPAlliance/url/pull/101/graphs/tree.svg?width=650&height=150&src=pr&token=jUkdNr0eJi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)](https://codecov.io/gh/CPPAlliance/url/pull/101?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #101      +/-   ##  
===========================================  
+ Coverage    96.36%   96.38%   +0.02%       
===========================================  
  Files           85       85                
  Lines         5336     5313      -23       
===========================================  
- Hits          5142     5121      -21       
+ Misses         194      192       -2       
```  
  
  
| [Impacted Files](https://codecov.io/gh/CPPAlliance/url/pull/101?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) | Coverage Δ | |  
|---|---|---|  
| [include/boost/url/rfc/impl/relative\_ref\_rule.ipp](https://codecov.io/gh/CPPAlliance/url/pull/101/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvcmZjL2ltcGwvcmVsYXRpdmVfcmVmX3J1bGUuaXBw) | `70.00% <0.00%> (-2.73%)` | :arrow_down: |  
| [include/boost/url/rfc/impl/uri\_rule.ipp](https://codecov.io/gh/CPPAlliance/url/pull/101/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvcmZjL2ltcGwvdXJpX3J1bGUuaXBw) | `76.92% <0.00%> (-1.65%)` | :arrow_down: |  
| [include/boost/url/rfc/impl/authority\_rule.ipp](https://codecov.io/gh/CPPAlliance/url/pull/101/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvcmZjL2ltcGwvYXV0aG9yaXR5X3J1bGUuaXBw) | `92.85% <0.00%> (-0.90%)` | :arrow_down: |  
| [include/boost/url/rfc/impl/uri\_reference\_rule.ipp](https://codecov.io/gh/CPPAlliance/url/pull/101/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvcmZjL2ltcGwvdXJpX3JlZmVyZW5jZV9ydWxlLmlwcA==) | `88.00% <0.00%> (-0.89%)` | :arrow_down: |  
| [include/boost/url/rfc/impl/hier\_part\_rule.ipp](https://codecov.io/gh/CPPAlliance/url/pull/101/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvcmZjL2ltcGwvaGllcl9wYXJ0X3J1bGUuaXBw) | `90.90% <0.00%> (-0.27%)` | :arrow_down: |  
| [include/boost/url/rfc/impl/userinfo\_rule.ipp](https://codecov.io/gh/CPPAlliance/url/pull/101/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvcmZjL2ltcGwvdXNlcmluZm9fcnVsZS5pcHA=) | `95.00% <0.00%> (-0.24%)` | :arrow_down: |  
| [include/boost/url/rfc/impl/relative\_part\_rule.ipp](https://codecov.io/gh/CPPAlliance/url/pull/101/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvcmZjL2ltcGwvcmVsYXRpdmVfcGFydF9ydWxlLmlwcA==) | `91.66% <0.00%> (-0.23%)` | :arrow_down: |  
| [include/boost/url/rfc/impl/port\_rule.ipp](https://codecov.io/gh/CPPAlliance/url/pull/101/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvcmZjL2ltcGwvcG9ydF9ydWxlLmlwcA==) | `97.22% <0.00%> (-0.15%)` | :arrow_down: |  
| [include/boost/url/rfc/impl/host\_rule.ipp](https://codecov.io/gh/CPPAlliance/url/pull/101/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvcmZjL2ltcGwvaG9zdF9ydWxlLmlwcA==) | `97.14% <0.00%> (-0.08%)` | :arrow_down: |  
| [include/boost/url/rfc/impl/paths\_rule.ipp](https://codecov.io/gh/CPPAlliance/url/pull/101/diff?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance#diff-aW5jbHVkZS9ib29zdC91cmwvcmZjL2ltcGwvcGF0aHNfcnVsZS5pcHA=) | `98.73% <0.00%> (-0.02%)` | :arrow_down: |  
| ... and [21 more](https://codecov.io/gh/CPPAlliance/url/pull/101/diff?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance) | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/CPPAlliance/url/pull/101?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/CPPAlliance/url/pull/101?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Last update [c62c4ed...73a3f93](https://codecov.io/gh/CPPAlliance/url/pull/101?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=CPPAlliance).

---

## Comment 4

> Username: vinniefalco  
> Created_at: 2022-01-12 20:06:32 UTC  
> Url: https://github.com/boostorg/url/pull/101#issuecomment-1011407741  

The current CML already does this, so the change is redundant.

---
