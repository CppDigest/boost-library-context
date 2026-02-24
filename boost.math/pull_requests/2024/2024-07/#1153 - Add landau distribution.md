# #1153 Add landau distribution [Closed]

> Username: tk-yoshimura  
> Created at: 2024-07-15 08:03:59 UTC  
> Updated at: 2024-07-26 05:44:12 UTC  
> Closed at: 2024-07-21 10:56:14 UTC  
> Url: https://github.com/boostorg/math/pull/1153  

### Reference issue  
https://github.com/boostorg/math/issues/1152  
  
### What does this implement/fix?  
Add Landau distribution.  
  
### Additional information  
This is an implementation of the following report I wrote:  
[DOI](https://doi.org/10.36227/techrxiv.171822215.53612870/v2)  
  
The scale parameter behavior of the Landau distribution is particular, and my implementation is Wolfram Alpha compliant.  
https://reference.wolfram.com/language/ref/LandauDistribution.html  
  
As of July 2024, the left tail values output by Wolfram Alpha have not converged to an asymptotic curve, which is different from my implementation.  
N[PDF[LandauDistribution[0, pi/2], -15/2], 20]    
![wolfram_negativeside_error](https://github.com/user-attachments/assets/504d5e25-50d9-449f-ba3b-577087225296)  
  
While every effort has been made to calculate the coefficients, this implementation should be fully verified.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-07-15 14:02:55 UTC  
> Updated_at: 2024-07-15 14:25:12 UTC  
> Url: https://github.com/boostorg/math/pull/1153#issuecomment-2228583940  

## [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1153?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 93.77%. Comparing base [(`5459e14`)](https://app.codecov.io/gh/boostorg/math/commit/5459e1485cf8fdbc917ac913023c0b7dbec26390?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`6503b3f`)](https://app.codecov.io/gh/boostorg/math/commit/6503b3f315664ef071a86f95ae3d8b5a71b5d69e?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/math/pull/1153/graphs/tree.svg?width=650&height=150&src=pr&token=26IDb1OAa7&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/math/pull/1153?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1153      +/-   ##  
===========================================  
- Coverage    93.77%   93.77%   -0.01%       
===========================================  
  Files          772      772                
  Lines        61352    61291      -61       
===========================================  
- Hits         57535    57474      -61       
  Misses        3817     3817                
```  
  
[see 1 file with indirect coverage changes](https://app.codecov.io/gh/boostorg/math/pull/1153/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/math/pull/1153?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1153?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [5459e14...6503b3f](https://app.codecov.io/gh/boostorg/math/pull/1153?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 2

> Username: mborland  
> Created_at: 2024-07-15 14:03:46 UTC  
> Url: https://github.com/boostorg/math/pull/1153#issuecomment-2228585770  

This looks really good so far and I approved the CI run so hopefully we'll get some results shortly. Can you add a doc page for this as well? A good example is https://github.com/boostorg/math/blob/develop/doc/distributions/binomial.qbk.

---

## Comment 3

> Username: tk-yoshimura  
> Created_at: 2024-07-16 15:37:48 UTC  
> Url: https://github.com/boostorg/math/pull/1153#issuecomment-2231255879  

I have no experience with quickbook, so I need more time to create the document.

---

## Comment 4

> Username: mborland  
> Created_at: 2024-07-16 15:41:44 UTC  
> Url: https://github.com/boostorg/math/pull/1153#issuecomment-2231263853  

> I have no experience with quickbook, so I need more time to create the document.  
  
No rush. It's a niche documentation system I believe used exclusively by boost.

---

## Comment 5

> Username: tk-yoshimura  
> Created_at: 2024-07-17 04:53:18 UTC  
> Updated_at: 2024-07-18 15:49:18 UTC  
> Url: https://github.com/boostorg/math/pull/1153#issuecomment-2232414810  

Please point out any deficiencies in the documentation.  
  
I generated the image for the document.  
Is this correct?  
  
## Definition  
![landau_define](https://github.com/user-attachments/assets/87f5bdc4-0888-4901-b712-3765cfe20d31)  
![landau_define](https://github.com/user-attachments/assets/f4c2d6d4-9013-4c19-93c2-0e39803a72d1)  
  
## Location and Scale Parameter  
![landau_loc](https://github.com/user-attachments/assets/6db2b8a8-2e69-44eb-bdee-8821395c1ba1)  
![landau_scale](https://github.com/user-attachments/assets/fc6b1903-4d23-446c-84ef-79d08abfdb6f)  
  
## FP64 Accuracy  
![landau_pdf_cpp_fp64_pdf](https://github.com/user-attachments/assets/ef265db1-b4a3-4101-acb3-77e769699e74)  
![landau_cdf_cpp_fp64_cdf](https://github.com/user-attachments/assets/bc6b24f6-c14d-4cac-8856-3daa90d451f2)  
![landau_cdf_cpp_fp64_ccdf](https://github.com/user-attachments/assets/8fc28aa0-5f3d-4548-a35b-b435f6244302)  
  
## FP128 Accuracy  
![landau_pdf_cpp_fp128_pdf](https://github.com/user-attachments/assets/24bcd168-b5b0-42e8-a3ee-e02e8b96228b)  
![landau_cdf_cpp_fp128_cdf](https://github.com/user-attachments/assets/4ddc75a3-f375-4d49-8120-1fe420db7a49)  
![landau_cdf_cpp_fp128_ccdf](https://github.com/user-attachments/assets/7e6e3256-e0fc-467e-82b8-2b6df44b109a)

---

## Comment 6

> Username: jzmaddock  
> Created_at: 2024-07-19 11:36:31 UTC  
> Url: https://github.com/boostorg/math/pull/1153#issuecomment-2238954399  

Just looking at this now, many thanks for submitting this PR, detailed comments to follow shortly...

---

## Comment 7

> Username: tk-yoshimura  
> Created_at: 2024-07-20 12:12:29 UTC  
> Url: https://github.com/boostorg/math/pull/1153#issuecomment-2241107214  

@jzmaddock   
Thank you for the correction.  
I am very honored to have been reviewed by you.  
  
Is there anything I should correct about this PR in the future?  
  
The behavior of the scale parameter is debatable.  
![landau_locbias](https://github.com/user-attachments/assets/938fb72a-036f-40f1-b9b5-4fe3fe69fc6d)  
  
From the scipy that calls this boost, only the standard Landau distribution is used, and this is modified for scale and location parameters similar to other distributions.  
This is not consistent with the WolframAlpha implementation or the parameters of the Levy Stable distribution.  
  
I was very torn about which way to go on this issue to avoid confusion, but I designed it to conform to the WolframAlpha implementation.

---

## Comment 8

> Username: jzmaddock  
> Created_at: 2024-07-20 15:52:39 UTC  
> Url: https://github.com/boostorg/math/pull/1153#issuecomment-2241190274  

> Is there anything I should correct about this PR in the future?  
  
I have one more fix to push (my mistake!), but also we need to complete the last bits of the docs, can you please push (to here is fine) the SVG version of the equation to `libs/math/doc/equations/`.  Also assuming the equation started out as LaTex, could you please add the LaTex source as a comment to the .qbk file?  It just speeds things up if any future maintenance is required.  
  
Do you have .svg's of the graphs?  If so could you push them to `libs/math/doc/graphs/` ?  If not, then if you could add the .png's there, I'll fix up the docs to point to them.  
  
>From the scipy that calls this boost, only the standard Landau distribution is used, and this is modified for scale and location parameters similar to other distributions.  
This is not consistent with the WolframAlpha implementation or the parameters of the Levy Stable distribution.  
  
>I was very torn about which way to go on this issue to avoid confusion, but I designed it to conform to the WolframAlpha implementation.  
  
That sounds perfectly reasonable: do you happen to know which Landau used in his original paper and/or which is most useful for particle physics?  Whichever choice is made it needs to be carefully documented - can you add a sentence or two to the start of the docs (just after the scale and location are introduced) explaining the choice made?  
  
Many thanks for the PR, and a nice job!  If you can push those extra bits, then I'll push those plus my one last change to the integration PR, and hopefully one last CI run should get this over the line!

---

## Comment 9

> Username: tk-yoshimura  
> Created_at: 2024-07-20 17:23:39 UTC  
> Updated_at: 2024-07-21 11:04:34 UTC  
> Url: https://github.com/boostorg/math/pull/1153#issuecomment-2241235830  

These are SVG version of the graphs.  
I'm not sure if these are appropriate to commit due to their large file size, so I'm uploading them here.  
  
## Define  
![define](https://github.com/user-attachments/assets/034361fb-886e-454f-b2e4-0ff2b9ed38c8)  
```tex  
\begin{equation*}   
    f(x; \mu, c)=\frac{1}{\pi c} \int_{0}^{\infty} \exp(-t) \cos \left( t \left( \frac{x-\mu}{c}\right) + \frac{2t}{\pi} \log \left( \frac{t}{c} \right)  \right) dt  
\end{equation*}  
```  
  
## Location and Scale Parameter  
![landau_loc](https://github.com/user-attachments/assets/0749184d-1f89-41f0-82e2-3ab107375f23)  
![landau_scale](https://github.com/user-attachments/assets/ae3ac834-f974-47f2-9d65-360358e98aec)  
  
<details>  
<summary>## FP64 Accuracy</summary>  
  
![landau_pdf_cpp_fp64_pdf](https://github.com/user-attachments/assets/af9f3ee8-a379-4f56-9960-168933fc1b39)  
![landau_cdf_cpp_fp64_cdf](https://github.com/user-attachments/assets/d81a20e9-5676-4805-ad0c-4aaf526a8dfa)  
![landau_cdf_cpp_fp64_ccdf](https://github.com/user-attachments/assets/16b58255-65bc-4779-a705-a4df575d89b5)  
</details>  
  
<details>  
<summary>## FP128 Accuracy</summary>  
  
![landau_pdf_cpp_fp128_pdf](https://github.com/user-attachments/assets/c5f7acd7-a746-4a26-a63c-eba4e03edf86)  
![landau_cdf_cpp_fp128_cdf](https://github.com/user-attachments/assets/b16a274b-4d0a-4ae3-ac3d-f4438c4f876e)  
![landau_cdf_cpp_fp128_ccdf](https://github.com/user-attachments/assets/4541cd5d-6ba3-475e-a936-8006c7be84c2)  
</details>

---

## Comment 10

> Username: jzmaddock  
> Created_at: 2024-07-20 18:53:03 UTC  
> Url: https://github.com/boostorg/math/pull/1153#issuecomment-2241260702  

Thanks @tk-yoshimura : I'd forgotten how large those SVG ULP plots get!  I'll sort those out tomorrow.

---

## Comment 11

> Username: jzmaddock  
> Created_at: 2024-07-21 10:56:15 UTC  
> Url: https://github.com/boostorg/math/pull/1153#issuecomment-2241565878  

Thanks @tk-yoshimura : integration PR has been merged!  
  
When the other distributions are all ready - maybe they are now?  Let me know and I'll work through the whole lot in one go.

---

## Comment 12

> Username: tk-yoshimura  
> Created_at: 2024-07-21 11:07:34 UTC  
> Url: https://github.com/boostorg/math/pull/1153#issuecomment-2241569068  

Thank you very much! @jzmaddock   
The other distributions have been corrected.

---
