# #89 GSoC20 Phase 1: Enhance CI workflows and switch to Github Actions [Merged]

> Username: coder3101  
> Created at: 2020-07-01 07:41:48 UTC  
> Updated at: 2020-07-11 11:57:29 UTC  
> Merged at: 2020-07-11 11:57:29 UTC  
> Closed at: 2020-07-11 11:57:29 UTC  
> Url: https://github.com/boostorg/ublas/pull/89  

More details of the work of this phase could be checked at my wiki https://github.com/BoostGSoC20/ublas/wiki  
  
This PR introduces the following:  
  
- Use Github Actions as CI Service  
- Runs Units tests on a wide array of toolchains versions across all major OS  
- Adds Code Format checking and Clang tidy checking on CI  
- Add an Auto review script for PR  
- Increases code-coverage to 95% for **only tensor** module.  
- Adds Sanitizers to check for address and undefined behaviours  
  
This PR removes the following:  
- OpenCL tests are not run  
- Remove .travis.yml  
- Remove .appveyor.yml  
  
  
Requirements  
  
- [ ]  Enable Github Actions from repository Settings for Github to actually enable CI for the repository.  
- [ ]  Format the code as per .clang-tidy file   
- [ ]  Fix the clang-tidy issues. (I will propose another PR as a fix)  
  
  
Useful links:  
- [Motivations and Goals](https://github.com/BoostGSoC20/ublas/wiki/Github-actions)  
- [Build Matrix](https://github.com/BoostGSoC20/ublas/wiki/Github-Builds)  
- [Code Formatting](https://github.com/BoostGSoC20/ublas/wiki/Github-code-format)  
- [Clang tidy checks and review](https://github.com/BoostGSoC20/ublas/wiki/Github-clang-tidy-review)  
- [Sanitizer Checks](https://github.com/BoostGSoC20/ublas/wiki/Github-sanitizer-checks)

---

## Review 1 [Changes requested]

> Username: bassoy  
> Created_at: 2020-07-03 14:14:05 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/ublas/pull/89#pullrequestreview-442440412  

@coder3101 I really like this pull request for using Github-Actions including many important checks using `clang-tidy` and `clang-format`. This will definitely elevate `Boost.uBlas`.  
  
I request one change: Let's keep the appveyor and travis file for code except `Boost.ublas.tensor` and use Github-Actions for `Boost.ublas.tensor`.

---

## Review 2 [Approved]

> Username: bassoy  
> Created_at: 2020-07-04 09:50:02 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/ublas/pull/89#pullrequestreview-442599906  

I am approving the pull request.  
@coder3101 would you like to add to the new created files a copyright with your name, see appveyor.yml ?

---

## Review 3 [Approved]

> Username: amitsingh19975  
> Created_at: 2020-07-08 10:47:08 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/ublas/pull/89#pullrequestreview-444626760  

I approving the changes.

---

## Comment 4

> Username: coder3101  
> Created_at: 2020-07-10 09:21:50 UTC  
> Updated_at: 2020-07-10 09:22:32 UTC  
> Url: https://github.com/boostorg/ublas/pull/89#issuecomment-656578374  

If an active PR which has originated from this same repository for the `develop` branch is synchronized. CI will run every action twice (once for the push to feature/branch and another for pr to develop). This can be solved by filtering so that only `master` and `develop` triggers.  
  
Benefits of this approach:  
- No twice build for actions in the above scenario.  
  
Drawbacks:  
- For any features/branch to use Github actions all actions scripts will need to be modified (add features/branch to list for trigger branch).  
  
My take is to keep things as is, We certainly don't want normal code PR to have changes in GitHub action scripts.  
  
I declare this PR finalized;

---
