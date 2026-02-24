# #2676 field.ipp: IWYU for <cstdint> [Closed]

> Username: ednolan  
> Created at: 2023-05-01 17:13:09 UTC  
> Updated at: 2023-05-15 00:50:21 UTC  
> Closed at: 2023-05-15 00:50:20 UTC  
> Url: https://github.com/boostorg/beast/pull/2676  

Previously, a file that included only boost/beast/http/fields.hpp would fail to compile on GCC 13.1.0 with the following error:  
  
```  
In file included from /boost/boost/beast/http/field.hpp:411,  
                 from /boost/boost/beast/http/fields.hpp:16,  
                 from /repro/boost_repro.cpp:1:  
/boost/boost/beast/http/impl/field.ipp:30:10: error: 'uint32_t' in namespace 'std' does not name a type; did you mean 'wint_t'?  
   30 |     std::uint32_t  
      |          ^~~~~~~~  
      |          wint_t  
```  
  
This was because boost/beast/http/impl/field.ipp relied on a transitive include that is no longer present in libstdc++ 13.  
  
This commit addresses the issue by adding a `<cstdint>` include to boost/beast/http/impl/field.ipp.

---

## Comment 1

> Username: vinniefalco  
> Created_at: 2023-05-01 17:14:39 UTC  
> Url: https://github.com/boostorg/beast/pull/2676#issuecomment-1529969659  

That should have been caught with this unit test: https://github.com/boostorg/beast/blob/b0996f099ac4cb024675b0b320f07e2297c3130c/test/beast/http/field.cpp#L11

---

## Comment 2

> Username: ednolan  
> Created_at: 2023-05-01 17:16:29 UTC  
> Url: https://github.com/boostorg/beast/pull/2676#issuecomment-1529971526  

My guess would be that the unit tests were never run with GCC 13. I don't see that compiler in the list of CI checks

---

## Comment 3

> Username: vinniefalco  
> Created_at: 2023-05-01 17:25:55 UTC  
> Url: https://github.com/boostorg/beast/pull/2676#issuecomment-1529980006  

of COURSE... the ONE configuration that we don't test... is the one that has the error :)

---

## Comment 4

> Username: ednolan  
> Created_at: 2023-05-01 17:27:45 UTC  
> Updated_at: 2023-05-01 17:27:57 UTC  
> Url: https://github.com/boostorg/beast/pull/2676#issuecomment-1529981709  

To be fair, GCC 13 only came out five days ago.

---

## Comment 5

> Username: vinniefalco  
> Created_at: 2023-05-01 17:28:17 UTC  
> Url: https://github.com/boostorg/beast/pull/2676#issuecomment-1529982244  

Oh! I thought it was older.. thanks

---

## Comment 6

> Username: royjacobson  
> Created_at: 2023-05-03 16:53:07 UTC  
> Url: https://github.com/boostorg/beast/pull/2676#issuecomment-1533377766  

Just had the same problem with `beast/core/file_base.hpp`. It might be a wider problem :/

---

## Comment 7

> Username: vinniefalco  
> Created_at: 2023-05-03 17:13:08 UTC  
> Url: https://github.com/boostorg/beast/pull/2676#issuecomment-1533414898  

we need to add gcc13 to the matrix and fix all the errors

---

## Comment 8

> Username: klemens-morgenstern  
> Created_at: 2023-05-12 07:23:26 UTC  
> Url: https://github.com/boostorg/beast/pull/2676#issuecomment-1545296401  

Can confirm from local install (fedora 38)

---

## Comment 9

> Username: codecov[bot]  
> Created_at: 2023-05-12 08:18:15 UTC  
> Updated_at: 2023-05-12 08:21:00 UTC  
> Url: https://github.com/boostorg/beast/pull/2676#issuecomment-1545362268  

## [Codecov](https://app.codecov.io/gh/boostorg/beast/pull/2676?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#2676](https://app.codecov.io/gh/boostorg/beast/pull/2676?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (283781e) into [develop](https://app.codecov.io/gh/boostorg/beast/commit/b0996f099ac4cb024675b0b320f07e2297c3130c?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (b0996f0) will **decrease** coverage by `0.03%`.  
> The diff coverage is `n/a`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/beast/pull/2676/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/beast/pull/2676?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #2676      +/-   ##  
===========================================  
- Coverage    92.96%   92.94%   -0.03%       
===========================================  
  Files          177      177                
  Lines        13651    13658       +7       
===========================================  
+ Hits         12691    12694       +3       
- Misses         960      964       +4       
```  
  
  
| [Impacted Files](https://app.codecov.io/gh/boostorg/beast/pull/2676?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/http/impl/field.ipp](https://app.codecov.io/gh/boostorg/beast/pull/2676?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9odHRwL2ltcGwvZmllbGQuaXBw) | `97.64% <ø> (ø)` | |  
  
... and [3 files with indirect coverage changes](https://app.codecov.io/gh/boostorg/beast/pull/2676/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/beast/pull/2676?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/beast/pull/2676?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [b0996f0...283781e](https://app.codecov.io/gh/boostorg/beast/pull/2676?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 10

> Username: klemens-morgenstern  
> Created_at: 2023-05-15 00:50:20 UTC  
> Url: https://github.com/boostorg/beast/pull/2676#issuecomment-1547052901  

Completed in #2682

---
