# #583 Port TuttleOFX extension : aligned [Open]

> Username: meshtag  
> Created at: 2021-03-17 08:04:04 UTC  
> Updated at: 2021-06-25 08:06:17 UTC  
> Url: https://github.com/boostorg/gil/pull/583  

### Description  
  
This pull request intends to port ```aligned``` extension from [TuttleOFX repository](https://github.com/tuttleofx/TuttleOFX/tree/develop/libraries/boostHack/boost/gil/extension/toolbox) to Gil as mentioned in #449 .  
  
I have tried not to change anything significant in actual extension code. However, I have reformatted test cases completely in order to align them with current practices followed by Gil.  
  
  
### Tasklist  
  
<!-- Add YOUR OWN TASK(s), especially if your PR is a work in progress -->  
  
- [x] Add test case(s)  
- [x] Ensure all CI builds pass  
- [ ] Review and approve

---

## Review 1 [Changes requested]

> Username: mloskot  
> Created_at: 2021-04-09 20:02:16 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/583#pullrequestreview-632748175  

This PR needs to receive similar changes as https://github.com/boostorg/gil/pull/581#pullrequestreview-632740268 and https://github.com/boostorg/gil/pull/582#pullrequestreview-632745026  
  
The tests for the feature need to pass in the CI builds but currently they fail,  
e.g. https://github.com/boostorg/gil/pull/583/checks?check_run_id=2182957498#step:6:626  
  
```  
====== BEGIN OUTPUT ======  
libs/gil/test/extension/toolbox/aligned.cpp(288): test 'gil::equal_pixels(obtained_center_middle_view, gil::view(expected_center_middle_img))' failed in function 'int main()'  
libs/gil/test/extension/toolbox/aligned.cpp(289): test 'gil::equal_pixels(obtained_left_top_view, gil::view(expected_left_top_img))' failed in function 'int main()'  
2 errors detected.  
  
EXIT STATUS: 2  
====== END OUTPUT ======  
```

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2021-04-10 06:08:25 UTC  
> Updated_at: 2021-04-15 09:30:39 UTC  
> Url: https://github.com/boostorg/gil/pull/583#issuecomment-817085798  

# [Codecov](https://codecov.io/gh/boostorg/gil/pull/583?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#583](https://codecov.io/gh/boostorg/gil/pull/583?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (d5ff162) into [develop](https://codecov.io/gh/boostorg/gil/commit/bbdce36a1f655ebf8332b775790673b197c8f1de?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (bbdce36) will **increase** coverage by `0.02%`.  
> The diff coverage is `85.00%`.  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #583      +/-   ##  
===========================================  
+ Coverage    78.72%   78.74%   +0.02%       
===========================================  
  Files          118      119       +1       
  Lines         5034     5054      +20       
===========================================  
+ Hits          3963     3980      +17       
- Misses        1071     1074       +3       
```

---

## Comment 3

> Username: meshtag  
> Created_at: 2021-04-10 07:59:31 UTC  
> Url: https://github.com/boostorg/gil/pull/583#issuecomment-817096988  

@mloskot , I will have to examine this more clearly for figuring out why this PR builds successfully on some configurations and fails for others. Currently, I am a bit busy working on my GSoC proposal and [kernel generation for Sobel operator](https://github.com/meshtag/gil/tree/kernel_generation_modified). I will try to fix this again after 13th April 2021, hence marking this PR as draft for now.

---

## Comment 4

> Username: mloskot  
> Created_at: 2021-04-10 11:29:39 UTC  
> Url: https://github.com/boostorg/gil/pull/583#issuecomment-817121461  

Absolutely no rush, take your time.

---
