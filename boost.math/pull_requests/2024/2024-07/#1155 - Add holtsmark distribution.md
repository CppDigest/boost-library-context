# #1155 Add holtsmark distribution [Closed]

> Username: tk-yoshimura  
> Created at: 2024-07-16 05:41:42 UTC  
> Updated at: 2024-07-26 15:03:41 UTC  
> Closed at: 2024-07-26 14:36:00 UTC  
> Url: https://github.com/boostorg/math/pull/1155  

### Reference issue  
https://github.com/boostorg/math/issues/1152  
  
### What does this implement/fix?  
Add Holtsmark distribution.  
  
### Additional information  
This is an implementation of the following report I wrote:  
[DOI](http://dx.doi.org/10.36227/techrxiv.172054657.73020014/v1)

---

## Comment 1

> Username: tk-yoshimura  
> Created_at: 2024-07-17 12:51:50 UTC  
> Updated_at: 2024-07-23 14:03:49 UTC  
> Url: https://github.com/boostorg/math/pull/1155#issuecomment-2233249782  

## Definition  
![texclip20240723230244](https://github.com/user-attachments/assets/5f971252-10d5-4443-8e23-171dba80ce31)  
```tex  
\begin{equation*}   
    f(x; \mu, c)=\frac{1}{2 \pi} \int_{-\infty}^{\infty} \exp( i t \mu - |c t|^{3/2} ) e^{-i x t} dt  
\end{equation*}  
```  
  
## Location and Scale Parameter  
![holtsmark_loc](https://github.com/user-attachments/assets/fab17510-87dd-4193-bcb3-875bea1787f4)  
![holtsmark_scale](https://github.com/user-attachments/assets/473da8cd-ecc0-4dab-9af4-4b988d526e7b)  
  
## FP64 Accuracy  
![holtsmark_pdf_cpp_fp64_pdf](https://github.com/user-attachments/assets/8fb11317-dab9-467d-9764-049a51475c90)  
![holtsmark_cdf_cpp_fp64_ccdf](https://github.com/user-attachments/assets/0cc386a4-6f1c-4f9f-b390-767c0c9c46eb)  
  
## FP128 Accuracy  
![holtsmark_pdf_cpp_fp128_pdf](https://github.com/user-attachments/assets/c3e3ba5b-d661-4eaa-a1cc-62598289bbe4)  
![holtsmark_cdf_cpp_fp128_ccdf](https://github.com/user-attachments/assets/18a37647-3e2f-41c3-8e8a-7d1564a626bb)  
  
## S&alpha;S Distributions  
![sass_pdf](https://github.com/user-attachments/assets/116cb7b8-3dcf-4ecd-8473-9f177d3a1b16)  
![sass_pdf_loglog](https://github.com/user-attachments/assets/3deb8c21-2764-4eb1-aa75-f857b92b85b1)

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2024-07-24 01:53:32 UTC  
> Updated_at: 2024-07-24 07:52:36 UTC  
> Url: https://github.com/boostorg/math/pull/1155#issuecomment-2246710780  

## [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1155?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 91.50%. Comparing base [(`5459e14`)](https://app.codecov.io/gh/boostorg/math/commit/5459e1485cf8fdbc917ac913023c0b7dbec26390?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`a2d109b`)](https://app.codecov.io/gh/boostorg/math/commit/a2d109be27ab0a1e0279bf56a998cdbd0d2f4016?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 1 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/math/pull/1155/graphs/tree.svg?width=650&height=150&src=pr&token=26IDb1OAa7&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/math/pull/1155?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1155      +/-   ##  
===========================================  
- Coverage    93.77%   91.50%   -2.28%       
===========================================  
  Files          772      774       +2       
  Lines        61352    64303    +2951       
===========================================  
+ Hits         57535    58839    +1304       
- Misses        3817     5464    +1647       
```  
  
[see 7 files with indirect coverage changes](https://app.codecov.io/gh/boostorg/math/pull/1155/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/math/pull/1155?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1155?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [5459e14...a2d109b](https://app.codecov.io/gh/boostorg/math/pull/1155?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2024-07-26 14:36:00 UTC  
> Url: https://github.com/boostorg/math/pull/1155#issuecomment-2252905346  

Superseded by https://github.com/boostorg/math/pull/1163

---
