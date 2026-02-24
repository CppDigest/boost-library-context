# #1121 fix the root cert used by example to match the fingerprint in the comment [Closed]

> Username: arvidn  
> Created at: 2018-05-08 18:40:25 UTC  
> Updated at: 2018-05-09 01:23:19 UTC  
> Closed at: 2018-05-09 01:23:19 UTC  
> Url: https://github.com/boostorg/beast/pull/1121  

The comment specifies the fingerprint of the certificate to be  
`‎de 28 f4 a4 ff e5 b9 2f a3 c5 03 d1 a3 49 a7 f9 96 2a 82 12`, but the actual certificate is identical to the one above. The certificate with the specified fingerprint can be found here:  
  
  https://www.geotrust.com/resources/root_certificates/certificates/GeoTrust_Global_CA.pem  
  
This patch updates the certificate to actually use the one the comment suggests it is.

---

## Comment 1

> Username: vinniefalco  
> Created_at: 2018-05-08 21:00:42 UTC  
> Url: https://github.com/boostorg/beast/pull/1121#issuecomment-387540897  

Not sure how that happened, thanks!

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2018-05-08 21:54:34 UTC  
> Url: https://github.com/boostorg/beast/pull/1121#issuecomment-387554680  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/1121?src=pr&el=h1) Report  
> Merging [#1121](https://codecov.io/gh/boostorg/beast/pull/1121?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/46e658a91bbda276d674a27c71ca56002b656817?src=pr&el=desc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/1121/graphs/tree.svg?width=650&height=150&src=pr&token=Gq6MFA9JRF)](https://codecov.io/gh/boostorg/beast/pull/1121?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #1121   +/-   ##  
========================================  
  Coverage    95.48%   95.48%             
========================================  
  Files          106      106             
  Lines        10686    10686             
========================================  
  Hits         10204    10204             
  Misses         482      482  
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/1121?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/1121?src=pr&el=footer). Last update [46e658a...11fc7c0](https://codecov.io/gh/boostorg/beast/pull/1121?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
