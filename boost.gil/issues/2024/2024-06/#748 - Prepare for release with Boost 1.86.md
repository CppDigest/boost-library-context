# #748 - Prepare for release with Boost 1.86 [Closed]

> Username: striezel  
> Created at: 2024-06-30 11:53:02 UTC  
> Updated at: 2024-08-23 23:54:08 UTC  
> Closed at: 2024-07-09 23:01:27 UTC  
> Url: https://github.com/boostorg/gil/issues/748  

Seeing that the last Boost release with changes in GIL was [Boost 1.80.0](https://www.boost.org/users/history/version_1_80_0.html) in August 2022 and seeing that some fixes and features have landed on the `develop` branch during that time, it is probably a good time to perform the next true merge of `develop` into `master` to prepare for the next release of GIL / Boost.  
  
Steps:  
1. [x]  Merge (@mloskot?)  
2. [x]  Build locally before `git push` _(optional, because the CI should catch any errors)_  
3. [x]  Push  
4. [x]  Review CI status  
5. [x]  Collect release notes to `RELEASES.md` (see #749)  
6. [x]  Copy release notes to https://github.com/boostorg/website/ (https://github.com/boostorg/website/pull/844)  
  
### Changes  
   
Below is an initial log of commits that will get merged from current `develop` into `master` for release with Boost 1.86 (as of 30 June 2024):  
  
```  
git log origin/master..develop --reverse --pretty=format:" | %cs | [%h](http://github.com/boostorg/gil/commit/%H) | %s |" | nl | sed -E 's/^\s/\|/g'  
```  
|    #   | Date       | Hash                                                                                        | Subject |  
| ------ | ---------- | ------------------------------------------------------------------------------------------- | ------- |  
|    1	 | 2022-08-10 | [8465d5463](http://github.com/boostorg/gil/commit/8465d5463168a507fdd16d600b62c3efa51269fd) | ci: partially replace Ubuntu 18.04 on GHA with Ubuntu 20.04 (#716) |  
|    2	 | 2022-08-12 | [1f89d622e](http://github.com/boostorg/gil/commit/1f89d622e3755d472232591ee5a341564af85d91) | ci: replace macOS 10.15 with macOS 11 in CI pipelines (#717) |  
|    3	 | 2022-10-05 | [135edcfad](http://github.com/boostorg/gil/commit/135edcfad336c226ab3341c5384283043dcd6606) | docs: Support newer version of Sphinx (#719) |  
|    4	 | 2022-10-06 | [3f7daa99b](http://github.com/boostorg/gil/commit/3f7daa99bac3d9f32bf8d47a257eb410cfe7378a) | docs: Fix HTML syntax |  
|    5	 | 2022-11-14 | [1df8c2407](http://github.com/boostorg/gil/commit/1df8c2407e71dd80d258b573d67925273e555810) | ci: Update actions/checkout in GitHub Actions workflows to v3 (#720) |  
|    6	 | 2023-02-07 | [712b827ed](http://github.com/boostorg/gil/commit/712b827edbe60a1eb6c13b1a677e7d2a4f74a116) | fix: Convolution in convolve_2d (#723) |  
|    7	 | 2023-02-07 | [d6e67f38c](http://github.com/boostorg/gil/commit/d6e67f38c548372e3d5c4e656c2d763c914bf918) | fix: Normalize Gaussian 2D kernel. (#725) |  
|    8	 | 2023-02-21 | [8f4cdcbcc](http://github.com/boostorg/gil/commit/8f4cdcbcce2be1cec30fad9bcf375ca3d8eee1b7) | fix: Fixed custom color converter in dynamic_factory and added corresponding (#726) |  
|    9	 | 2023-02-22 | [ecea33a90](http://github.com/boostorg/gil/commit/ecea33a9051e607dd05db199fdbe6ccdb31a1a5a) | fix: Make locator.hpp self contained (#729) |  
|   10	 | 2023-03-16 | [eabd679f6](http://github.com/boostorg/gil/commit/eabd679f632be3170d98145fcc3b49e85df7cc4b) | fix: Documentation (#731) |  
|   11	 | 2023-10-01 | [e24c87fde](http://github.com/boostorg/gil/commit/e24c87fde24a4027bc7a0a5c873ff18c39097128) | ci: ubuntu-18.04 environment is deprecated (#738) |  
|   12	 | 2023-12-25 | [3a4d45443](http://github.com/boostorg/gil/commit/3a4d45443d90398df7b3cd627d5fa344a10fc091) | ci: update actions/checkout in GitHub Actions workflows to v4 (#739) |  
|   13	 | 2023-12-25 | [822c19ee7](http://github.com/boostorg/gil/commit/822c19ee7d1db079603f75ad544446523eec6847) | ci: fix Python package in coverage workflow (#740) |  
|   14	 | 2024-01-27 | [322c4e2e1](http://github.com/boostorg/gil/commit/322c4e2e191458383db0f2873dd3301f05a7d137) | Updated Boost.Filesystem usage. (#741) |  
|   15	 | 2024-04-18 | [2c3ee866b](http://github.com/boostorg/gil/commit/2c3ee866b614d3c0695ae5de5931c366466b559f) | Portable minmax Win32-API workaround #744 (#745) |  
|   16	 | 2024-05-10 | [4fec872fe](http://github.com/boostorg/gil/commit/4fec872feb14897c41c95583c595ac0836d1ba51) | feat: Guard boost::filesystem usage (#743) |  
|   17	 | 2024-05-13 | [abb561a22](http://github.com/boostorg/gil/commit/abb561a226b02e2b7cb4a18a11050a0e60aaa4d9) | fix: Wrong buffer size in path string conversion functions (#746) |  
|   18	 | 2024-05-17 | [481b6e7c1](http://github.com/boostorg/gil/commit/481b6e7c14a289df86856fb0c755b3947eee3fa7) | feat: add tell() and error() functions to istream + ostream devices (#747) |  
  
_(If this looks familiar, then because it was shamelessly copied from #667, the issue for the Boost 1.80 release.)_  
  
# Timeline  
  
According to the calender on <https://www.boost.org/community/index.html> the following dates are relevant for the release:  
  
| Date       | Subject       | Description |  
| ---------- | ------------- | ----------- |  
| 2024-06-24 | Boost 1.86.0 closed for new libraries and breaking changes | Release branch is closed for new libraries and breaking changes to existing libraries. Still open for bug fixes and other routine changes to all libraries without release manager review. |  
| 2024-07-03 | Boost 1.86.0 closed for major changes | Release closed for major code changes. Still open for serious problem fixes and docs changes without release manager review. |  
| 2024-07-10 | Boost 1.86.0 closed for beta | Release closed for all changes |  
| 2024-07-17 | Boost 1.86.0 beta | Beta posted for download. |  
| 2024-07-18 | Boost 1.86.0 open for bug fixes | Release open for bug fixes and documentation updates. Other changes by permission of a release manager. |  
| 2024-08-07 | Boost 1.86.0 closed | Release closed for all changes |  
| 2024-08-14 | Boost 1.86.0 release | Release posted for download. |  
  
We are already past the first date in that list, but a first glance at the commit list above shows no obvious breaking changes, so it probably is still fine. There are still a few days left before the beta is closed for major changes, so we should still be able to get the changes from `develop` in right in time before the beta.

---

## Comment 1

> Username: mloskot  
> Created at: 2024-07-07 12:33:31 UTC  
> Updated at: 2024-07-07 12:34:15 UTC  
> Url: https://github.com/boostorg/gil/issues/748#issuecomment-2212435170  

## Preparing for merge  
  
```  
git switch master  
cd libs/gil  
git switch develop  
../../b2 cxxstd=14 test  
```  
  
All tests passed.

---

## Comment 2

> Username: mloskot  
> Created at: 2024-07-07 12:34:04 UTC  
> Updated at: 2024-07-07 12:38:12 UTC  
> Url: https://github.com/boostorg/gil/issues/748#issuecomment-2212435315  

## Merge  
  
```  
$ git merge --no-ff develop  
Merge made by the 'ort' strategy.  
 .azure-pipelines.yml                                             | 71 -----------------------------------------------------------------------  
 .ci/azure-pipelines/steps-check-cmake.yml                        |  9 ---------  
 .ci/azure-pipelines/steps-cmake-build-and-test.yml               | 51 ---------------------------------------------------  
 .ci/azure-pipelines/steps-install-boost.yml                      | 41 -----------------------------------------  
 .ci/azure-pipelines/steps-install-conan.yml                      | 17 -----------------  
 .ci/azure-pipelines/steps-install-gcc.yml                        | 18 ------------------  
 .github/workflows/ci.yml                                         | 48 +++++++++++++++++++++++++++++++-----------------  
 .github/workflows/coverage.yml                                   |  5 ++---  
 .github/workflows/docs.yaml                                      |  2 +-  
 CMakeLists.txt                                                   | 26 ++++++++++++++++++++++----  
 README.md                                                        |  8 ++++----  
 RELEASES.md                                                      | 23 +++++++++++++++++++++++  
 doc/_static/boost.css                                            | 19 +++++++++++++++++++  
 doc/_templates/layout.html                                       |  3 +++  
 doc/design/dynamic_image.rst                                     |  6 +++---  
 doc/io.rst                                                       |  6 ++++--  
 include/boost/gil/detail/math.hpp                                |  2 +-  
 include/boost/gil/extension/dynamic_image/image_view_factory.hpp | 20 ++++++--------------  
 include/boost/gil/extension/histogram/std.hpp                    |  4 ++--  
 include/boost/gil/extension/io/tiff/detail/read.hpp              |  6 +++---  
 include/boost/gil/histogram.hpp                                  | 12 ++++++------  
 include/boost/gil/image_processing/convolve.hpp                  |  2 +-  
 include/boost/gil/image_processing/histogram_equalization.hpp    |  8 ++++----  
 include/boost/gil/image_processing/histogram_matching.hpp        |  8 ++++----  
 include/boost/gil/image_processing/numeric.hpp                   | 15 ++++++++++-----  
 include/boost/gil/io/device.hpp                                  | 33 ++++++++++++++++++++++++++++++++-  
 include/boost/gil/io/path_spec.hpp                               | 23 +++++++++++++++--------  
 test/core/image_processing/convolve_2d.cpp                       | 93 +++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++  
 test/core/image_processing/simple_kernels.cpp                    | 17 ++++++++++-------  
 test/extension/dynamic_image/image_view_factory.cpp              | 67 +++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++  
 test/extension/io/png/png_file_format_test.cpp                   |  6 +++---  
 31 files changed, 369 insertions(+), 300 deletions(-)  
 delete mode 100644 .azure-pipelines.yml  
 delete mode 100644 .ci/azure-pipelines/steps-check-cmake.yml  
 delete mode 100644 .ci/azure-pipelines/steps-cmake-build-and-test.yml  
 delete mode 100644 .ci/azure-pipelines/steps-install-boost.yml  
 delete mode 100644 .ci/azure-pipelines/steps-install-conan.yml  
 delete mode 100644 .ci/azure-pipelines/steps-install-gcc.yml  
```  
  
## Post-merge build  
  
Locally, on Linux with GCC 14  
  
```  
cd libs/gil  
../../b2 cxxstd=14 test  
../../b2 cxxstd=14 variant=release address-model=64 test ## double check  
```  
  
All tests passed.

---

## Comment 3

> Username: mloskot  
> Created at: 2024-07-07 12:39:56 UTC  
> Url: https://github.com/boostorg/gil/issues/748#issuecomment-2212436799  

Merge of `develop` to `master` pushed.  
  
/cc @striezel @sdebionne

---

## Comment 4

> Username: striezel  
> Created at: 2024-07-08 00:27:02 UTC  
> Url: https://github.com/boostorg/gil/issues/748#issuecomment-2212668248  

Thank you.  
  
The merge happened before the beta is closed, so people will have the chance to test these changes when the beta is posted for download. :)  
  
By the way, the CI on the `master` branch passed, so I marked the task "Review CI status" as done.

---

## Comment 5

> Username: mloskot  
> Created at: 2024-07-08 21:56:48 UTC  
> Url: https://github.com/boostorg/gil/issues/748#issuecomment-2215416913  

I think this issue has been complete, hasn't it? And, thanks to your priceless help @striezel, we are good to be released with Boost 1.86, aren't we?  
  
https://github.com/boostorg/gil/milestones/Boost%201.86 still lists numerous open PR-s/issues but those are drafts or unfinished work, so those will be moved to another milestone.

---

## Comment 6

> Username: marco-langer  
> Created at: 2024-07-09 06:13:28 UTC  
> Url: https://github.com/boostorg/gil/issues/748#issuecomment-2216662976  

I think that I also have some open PRs / drafts which are older than 1-2 years.  
  
I will have a look at them and close them if I think they will be never finished.

---

## Comment 7

> Username: mloskot  
> Created at: 2024-07-09 08:07:10 UTC  
> Url: https://github.com/boostorg/gil/issues/748#issuecomment-2216889738  

@marco-langer We all have unfinished projects out there, so please don't worry about closing any of your PR-s. If we keep them open, that may catch an eye of a contributor who would be interested to pick up and continue any unfinished work.

---

## Comment 8

> Username: striezel  
> Created at: 2024-07-09 23:01:28 UTC  
> Updated at: 2024-07-09 23:02:28 UTC  
> Url: https://github.com/boostorg/gil/issues/748#issuecomment-2218886813  

> I think this issue has been complete, hasn't it? And, thanks to your priceless help @striezel, we are good to be released with Boost 1.86, aren't we?  
  
Yes, the issue is completed and GIL bsically is in a good shape for the release of Boost 1.86.0. I'll close the issue.  
  
> https://github.com/boostorg/gil/milestones/Boost%201.86 still lists numerous open PR-s/issues but those are drafts or unfinished work, so those will be moved to another milestone.  
  
Maybe. The Boost 1.86.0 beta is closed for changes (in my timezone it's already 2024-07-10) and will be posted for download next week.  
  
However, there will still be a few weeks time after that for bugfixes and documentation changes. Maybe some of the issues of that milestone will get fixed during that time. In that case another true merge from develop to master branch would be required before the release is closed for all changes. For example, I still would like to get the changes from #751 into Boost 1.86.0, because there are some awkward typos in there. :laughing:

---

## Comment 9

> Username: mloskot  
> Created at: 2024-08-20 13:00:35 UTC  
> Url: https://github.com/boostorg/gil/issues/748#issuecomment-2298804331  

My apologies for no reaction, summer holidays period is not the best time for attending releases.

---

## Comment 10

> Username: striezel  
> Created at: 2024-08-23 23:54:06 UTC  
> Url: https://github.com/boostorg/gil/issues/748#issuecomment-2307936001  

> My apologies for no reaction  
  
Don't worry. I think all the important stuff (that is, bugfixes and features) got merged in time before the 1.86 release. And for the rest there's always the next release.
