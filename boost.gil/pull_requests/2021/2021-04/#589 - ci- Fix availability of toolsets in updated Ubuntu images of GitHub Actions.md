# #589 ci: Fix availability of toolsets in updated Ubuntu images of GitHub Actions [Merged]

> Username: mloskot  
> Created at: 2021-04-02 10:42:30 UTC  
> Updated at: 2021-04-02 13:53:07 UTC  
> Merged at: 2021-04-02 13:53:03 UTC  
> Closed at: 2021-04-02 13:53:03 UTC  
> Url: https://github.com/boostorg/gil/pull/589  

<!-- Pull Requests MUST come from topic branch based on develop, and NEVER on `master) --->  
  
### Description  
  
The `compiler` property should trigger creation of `user-config.jam`  
with the expected GCC 8 defined.  
  
Something has changed (see image README-s below) on the GitHub Actions images or Boost.Build  
and GCC 8 build job started failing:  
  
   /home/runner/work/gil/boost-root/tools/build/src/tools/gcc.jam:203: in gcc.init from module gcc  
   error: toolset gcc initialization:  
   error: version '8' requested but 'g++-8' not found and version '7.5.0' of default 'g++' does not match  
  
e.g. https://github.com/boostorg/gil/pull/562/checks?check_run_id=2246393722  
  
<!-- What does this pull request do? -->  
  
### References  
  
<!-- Any links related to this PR: issues, other PRs, mailing list threads, StackOverflow questions, etc. -->  
  
- Attempt to fix https://github.com/boostorg/gil/pull/562#issuecomment-812025806 (/cc @meshtag)  
- Default toolsets seem to have been updated  
   - https://github.com/actions/virtual-environments/blob/main/images/linux/Ubuntu1804-README.md   
   - https://github.com/actions/virtual-environments/blob/main/images/linux/Ubuntu2004-README.md  
  
### Tasklist  
  
<!-- Add YOUR OWN TASK(s), especially if your PR is a work in progress -->  
  
- [x] Ensure all CI builds pass - the `posix (clang, clang++-3.5, 11,14, _GLIBCXX_USE_CXX11_ABI=0, ubuntu-16.04, clang-3.5) ` job failed for different reasons

---
