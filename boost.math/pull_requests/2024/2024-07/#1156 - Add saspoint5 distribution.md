# #1156 Add saspoint5 distribution [Closed]

> Username: tk-yoshimura  
> Created at: 2024-07-16 07:45:55 UTC  
> Updated at: 2024-07-26 15:04:02 UTC  
> Closed at: 2024-07-26 14:36:15 UTC  
> Url: https://github.com/boostorg/math/pull/1156  

### Reference issue  
https://github.com/boostorg/math/issues/1152  
  
### What does this implement/fix?  
Add S&alpha;S point5 distribution.  
  
### Additional information  
This is an implementation of the following report I wrote:  
[DOI](http://dx.doi.org/10.36227/techrxiv.172055253.37208198/v1)

---

## Comment 1

> Username: NAThompson  
> Created_at: 2024-07-16 14:15:57 UTC  
> Url: https://github.com/boostorg/math/pull/1156#issuecomment-2231019271  

@tk-yoshimura : You may wish to push just one PR past the finish line before opening new ones; we may have feedback that applies to all of these.

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2024-07-16 14:22:32 UTC  
> Updated_at: 2024-07-23 21:49:10 UTC  
> Url: https://github.com/boostorg/math/pull/1156#issuecomment-2231051491  

## [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1156?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 91.48%. Comparing base [(`5459e14`)](https://app.codecov.io/gh/boostorg/math/commit/5459e1485cf8fdbc917ac913023c0b7dbec26390?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`e407cad`)](https://app.codecov.io/gh/boostorg/math/commit/e407cadd0d9fcbf6015dc7c7202942cd1ee02282?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 1 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/math/pull/1156/graphs/tree.svg?width=650&height=150&src=pr&token=26IDb1OAa7&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/math/pull/1156?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1156      +/-   ##  
===========================================  
- Coverage    93.77%   91.48%   -2.30%       
===========================================  
  Files          772      774       +2       
  Lines        61352    64303    +2951       
===========================================  
+ Hits         57535    58827    +1292       
- Misses        3817     5476    +1659       
```  
  
[see 8 files with indirect coverage changes](https://app.codecov.io/gh/boostorg/math/pull/1156/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/math/pull/1156?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1156?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [5459e14...e407cad](https://app.codecov.io/gh/boostorg/math/pull/1156?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 3

> Username: tk-yoshimura  
> Created_at: 2024-07-16 15:14:54 UTC  
> Updated_at: 2024-07-16 15:18:24 UTC  
> Url: https://github.com/boostorg/math/pull/1156#issuecomment-2231192373  

Thank you. @NAThompson  
I would like to prioritize modifying the Landau distribution.

---

## Comment 4

> Username: tk-yoshimura  
> Created_at: 2024-07-17 12:53:33 UTC  
> Updated_at: 2024-07-23 14:16:43 UTC  
> Url: https://github.com/boostorg/math/pull/1156#issuecomment-2233253256  

## Definition  
![texclip20240723231448](https://github.com/user-attachments/assets/29bfd362-3200-4527-a82d-77525ff635be)  
```tex  
\begin{equation*}   
    f(x; \mu, c)=\frac{1}{2 \pi} \int_{-\infty}^{\infty} \exp( i t \mu - \sqrt{|c t|} ) e^{-i x t} dt  
\end{equation*}  
```  
  
## Location and Scale Parameter  
![saspoint5_loc](https://github.com/user-attachments/assets/3198a391-d946-422c-8a4b-4a332f03c9a7)  
![saspoint5_scale](https://github.com/user-attachments/assets/a15f3e6e-2210-4ad0-8af8-5fc912b26359)  
  
## FP64 Accuracy  
![saspoint5_pdf_cpp_fp64_pdf](https://github.com/user-attachments/assets/ba29ffa2-8ed2-478d-a9bd-9ae80213ed73)  
![saspoint5_cdf_cpp_fp64_ccdf](https://github.com/user-attachments/assets/e0c29a69-7a53-4999-b885-f21a59f0a5cc)  
  
## FP128 Accuracy  
![saspoint5_pdf_cpp_fp128_pdf](https://github.com/user-attachments/assets/bfee545c-b07e-45a6-89ba-cf6660fd4611)  
![saspoint5_cdf_cpp_fp128_ccdf](https://github.com/user-attachments/assets/76d9306b-5dca-4f20-9a32-62c7cbdefb2e)

---

## Comment 5

> Username: jzmaddock  
> Created_at: 2024-07-26 14:36:15 UTC  
> Url: https://github.com/boostorg/math/pull/1156#issuecomment-2252905883  

Superseded by https://github.com/boostorg/math/pull/1163

---
