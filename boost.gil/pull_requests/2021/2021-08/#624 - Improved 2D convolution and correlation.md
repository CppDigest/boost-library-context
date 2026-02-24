# #624 Improved 2D convolution and correlation [Open]

> Username: meshtag  
> Created at: 2021-08-05 16:00:46 UTC  
> Updated at: 2022-06-05 21:15:49 UTC  
> Url: https://github.com/boostorg/gil/pull/624  

<!-- Pull Requests MUST come from topic branch based on develop, and NEVER on `master) --->  
  
### Description  
Fixes #609   
Fixes #610   
Depends on #623   
<!-- What does this pull request do? -->  
  
### References  
  
<!-- Any links related to this PR: issues, other PRs, mailing list threads, StackOverflow questions, etc. -->  
  
### Tasklist  
  
<!-- Add YOUR OWN TASK(s), especially if your PR is a work in progress -->  
  
- [x] Add test case(s)  
- [ ] Ensure all CI builds pass  
- [ ] Review and approve

---

## Comment 1

> Username: meshtag  
> Created_at: 2021-08-16 17:58:26 UTC  
> Updated_at: 2021-08-20 12:32:01 UTC  
> Url: https://github.com/boostorg/gil/pull/624#issuecomment-899705577  

Script used for comprehensive testing is [here](https://github.com/meshtag/gil/blob/check_build/test/extension/numeric/convolve_2d.cpp) and folder containing expected, source images for those tests is [here](https://drive.google.com/drive/folders/15uIi1OK_vSkLb8IAdw-00l5EpACPPMDE?usp=sharing).

---

## Comment 2

> Username: mloskot  
> Created_at: 2022-06-03 06:50:40 UTC  
> Url: https://github.com/boostorg/gil/pull/624#issuecomment-1145644833  

@meshtag Could you tell what is the status of this PR? Is this going to be picked up or drop it and close it?

---

## Comment 3

> Username: meshtag  
> Created_at: 2022-06-03 09:19:51 UTC  
> Url: https://github.com/boostorg/gil/pull/624#issuecomment-1145770553  

I will try to resolve merge conflicts and update this PR with latest version of the repository by the end of this weekend (will ping you once these PRs are ready for review). As far as usability is concerned, it was in a perfect working condition during my GSoC coding period. Same goes for #612 and #613 as well.

---

## Comment 4

> Username: mloskot  
> Created_at: 2022-06-03 09:29:01 UTC  
> Url: https://github.com/boostorg/gil/pull/624#issuecomment-1145778067  

Sounds good. Yes, there has been lots of activity hence the conflicts.  
If you will have trouble with resolving them and need help, let me know.

---

## Comment 5

> Username: codecov[bot]  
> Created_at: 2022-06-05 05:44:18 UTC  
> Url: https://github.com/boostorg/gil/pull/624#issuecomment-1146746671  

# [Codecov](https://codecov.io/gh/boostorg/gil/pull/624?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#624](https://codecov.io/gh/boostorg/gil/pull/624?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (c2c614d) into [develop](https://codecov.io/gh/boostorg/gil/commit/843ea374b69dc90b60d289e308c4e35319e8be58?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (843ea37) will **decrease** coverage by `3.61%`.  
> The diff coverage is `12.45%`.  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #624      +/-   ##  
===========================================  
- Coverage    78.76%   75.15%   -3.62%       
===========================================  
  Files          117      117                
  Lines         5030     5296     +266       
===========================================  
+ Hits          3962     3980      +18       
- Misses        1068     1316     +248       
```

---

## Comment 6

> Username: meshtag  
> Created_at: 2022-06-05 21:15:31 UTC  
> Updated_at: 2022-06-05 21:15:49 UTC  
> Url: https://github.com/boostorg/gil/pull/624#issuecomment-1146884842  

@mloskot , I have resolved merge conflicts in above mentioned PRs but will have to obtain a closer look to examine why some CI builds are failing in this one. I'll try to do this in the coming week.

---
