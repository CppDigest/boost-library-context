# #1154 Add map-airy distribution [Closed]

> Username: tk-yoshimura  
> Created at: 2024-07-15 18:26:08 UTC  
> Updated at: 2024-07-26 15:03:28 UTC  
> Closed at: 2024-07-26 14:35:42 UTC  
> Url: https://github.com/boostorg/math/pull/1154  

### Reference issue  
https://github.com/boostorg/math/issues/1152  
  
### What does this implement/fix?  
Add Map-Airy distribution.  
  
### Additional information  
This is an implementation of the following report I wrote:  
[DOI](http://dx.doi.org/10.36227/techrxiv.172053942.27675733/v1)

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-07-15 19:31:02 UTC  
> Updated_at: 2024-07-24 16:08:32 UTC  
> Url: https://github.com/boostorg/math/pull/1154#issuecomment-2229233925  

## [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1154?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 91.50%. Comparing base [(`5459e14`)](https://app.codecov.io/gh/boostorg/math/commit/5459e1485cf8fdbc917ac913023c0b7dbec26390?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`a5ed6ba`)](https://app.codecov.io/gh/boostorg/math/commit/a5ed6bae8daf6b28106c8833ee78fc6e46f78a0d?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 49 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/math/pull/1154/graphs/tree.svg?width=650&height=150&src=pr&token=26IDb1OAa7&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/math/pull/1154?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1154      +/-   ##  
===========================================  
- Coverage    93.77%   91.50%   -2.28%       
===========================================  
  Files          772      774       +2       
  Lines        61352    64303    +2951       
===========================================  
+ Hits         57535    58839    +1304       
- Misses        3817     5464    +1647       
```  
  
[see 7 files with indirect coverage changes](https://app.codecov.io/gh/boostorg/math/pull/1154/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/math/pull/1154?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1154?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [5459e14...a5ed6ba](https://app.codecov.io/gh/boostorg/math/pull/1154?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 2

> Username: tk-yoshimura  
> Created_at: 2024-07-17 12:49:57 UTC  
> Updated_at: 2024-07-20 17:39:22 UTC  
> Url: https://github.com/boostorg/math/pull/1154#issuecomment-2233246185  

## Definition  
![texclip20240718133322](https://github.com/user-attachments/assets/9782a6db-a9fa-475b-b8fc-b57d682589ff)  
```tex  
\begin{equation*}   
    f(x; \mu=0, c=1/\sqrt[3]{18}) = 2 \exp \left( \frac{2}{3} x^3 \right) \left( -x \mathrm{Ai}(x^2) - \mathrm{Ai}'(x^2) \right)  
\end{equation*}  
```  
  
## Location and Scale Parameter  
![mapairy_loc](https://github.com/user-attachments/assets/2c69a001-9bc2-4d69-b24f-3b1d9cb63376)  
![mapairy_scale](https://github.com/user-attachments/assets/f5db73f0-4c98-439a-be2c-a4b8659853e2)  
  
## FP64 Accuracy  
![mapairy_pdf_cpp_fp64_pdf](https://github.com/user-attachments/assets/12749ab4-754c-458c-94ae-773e99895797)  
![mapairy_cdf_cpp_fp64_cdf](https://github.com/user-attachments/assets/c68aa2f7-39d1-4b2c-80d9-26a08b187b93)  
![mapairy_cdf_cpp_fp64_ccdf](https://github.com/user-attachments/assets/928433f1-8604-475b-9eab-ac001aab8048)  
  
## FP128 Accuracy  
![mapairy_pdf_cpp_fp128_pdf](https://github.com/user-attachments/assets/b2a15488-935a-407a-bf60-e0a58036be1a)  
![mapairy_cdf_cpp_fp128_cdf](https://github.com/user-attachments/assets/b7358b46-8dd5-431c-b507-eeabdc57d2c0)  
![mapairy_cdf_cpp_fp128_ccdf](https://github.com/user-attachments/assets/ee5fb859-3acb-4a4d-bb35-e91a4ad444f8)

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2024-07-25 16:59:39 UTC  
> Url: https://github.com/boostorg/math/pull/1154#issuecomment-2250986001  

Thanks @tk-yoshimura, just doing some integration testing on this now...

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2024-07-26 14:35:42 UTC  
> Url: https://github.com/boostorg/math/pull/1154#issuecomment-2252904712  

Superseded by https://github.com/boostorg/math/pull/1163

---
