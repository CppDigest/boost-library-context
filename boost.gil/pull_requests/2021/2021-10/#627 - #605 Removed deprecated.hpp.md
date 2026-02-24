# #627 #605 Removed deprecated.hpp [Merged]

> Username: yogsothoth  
> Created at: 2021-10-29 06:33:19 UTC  
> Updated at: 2022-04-21 18:54:01 UTC  
> Merged at: 2022-04-06 09:42:56 UTC  
> Closed at: 2022-04-06 09:42:57 UTC  
> Url: https://github.com/boostorg/gil/pull/627  

### Description  
Remove /include/boost/gil/deprecated.hpp  
Remove include of deprecated.hpp from include/boost/gil.hpp  
  
### References  
  
See [Issue #605](https://github.com/boostorg/gil/issues/605)  
  
Note: [PR #625](https://github.com/boostorg/gil/pull/625) was proposed but not merged, as CI pipelines broke. The corresponding build logs seem to have been cleaned up; this PR should trigger a new build and produce new logs to inspect.  
  
Environment:  
- clang 11.0.1  
- FreeBSD 13.0-RELEASE-p4  
- x86_64  
  
### Tasklist  
  
- [X] Remove file include/boost/gil/deprecated.hpp  
- [X] Remove include from include/boost/gil.hpp  
- [X] Compile and run all tests with b2 (cxxstd=11,14,17,2a)   
- [X] Compare build output with a run w/deprecated.hpp  
- [ ] Ensure all CI builds pass  
- [x] Review and approve

---

## Comment 1

> Username: yogsothoth  
> Created_at: 2021-10-29 07:08:57 UTC  
> Url: https://github.com/boostorg/gil/pull/627#issuecomment-954485202  

The CI builds passed for:  
- win2016_vs2017_cxx14_cmake  
- win2016_vs2017_cxx17_cmake  
- macos1014_xcode11_cmake  
  
However, two pipelines need attention:  
- ubuntu_1604_gcc6_cxx14_cmake  
- ubuntu_1604_gcc8_cxx14_cmake  
  
The image seems to be missing:  
`##[warning]An image label with the label ubuntu-16.04 does not exist.  
  
,##[error]The remote provider was unable to process the request.`  
  
(the builds were automatically cancelled here)  
  
Not sure how I can help here?

---

## Comment 2

> Username: meshtag  
> Created_at: 2021-10-29 08:12:11 UTC  
> Updated_at: 2021-10-29 08:17:02 UTC  
> Url: https://github.com/boostorg/gil/pull/627#issuecomment-954539441  

Mentioning [this](https://github.com/actions/virtual-environments/issues/3287) for further info. (/cc @mloskot @lpranam @simmplecoder)

---

## Review 3 [Approved]

> Username: lpranam  
> Created_at: 2022-04-06 09:33:51 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/627#pullrequestreview-933174006  

looks good to me :)

---

## Review 4 [Approved]

> Username: mloskot  
> Created_at: 2022-04-06 09:41:35 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/627#pullrequestreview-933184870  

Despite the CI is still not working well, let's merge.

---
