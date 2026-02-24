# #459 Replace Boost.Test with Boost.LightweightTest in test/ [Merged]

> Username: mloskot  
> Created at: 2020-03-21 00:16:01 UTC  
> Updated at: 2020-03-26 19:37:47 UTC  
> Merged at: 2020-03-21 21:53:09 UTC  
> Closed at: 2020-03-21 21:53:10 UTC  
> Url: https://github.com/boostorg/gil/pull/459  

<!-- Pull Requests MUST come from topic branch based on develop, and NEVER on `master) --->  
  
### Description  
  
Motivation is to:  
- use single, simpler and light test framework  
- eliminate dependency on libraries like Boost.MPL  
- achieve faster compilation times for CI builds (20% seems feasible)  
- have test programs easy to run and debug  
- avoid macros  
  
Split some of large or all-in-one test `.cpp` files into smaller ones.  
Improve `test/extension/io/Jamfile` with separate format-specific targets.  
Remove outdated FIXME-s for bugs that have been already fixed.  
Fix off-by-one bug in test/core/test_fixture.hpp generators.  
Minor corrections and tidying up.  
  
Add missing test assertions to numeric extension tests.  
<!-- What does this pull request do? -->  
  
### References  
  
* Use of both Boost.Test and Boost.LightweightTest was discussed/agreed in #57. This PR switches to the Boost.LT as the single testing framework used in GIL. Thus, nothing new, just refactoring overhaul.  
* Also fixes #458  
* The IO to be continued as part of #461  
* This is a change of direction, reverts #417  
  
### Tasklist  
  
<!-- Add YOUR OWN TASK(s), especially if your PR is a work in progress -->  
  
- [x] Switch test/core  
- [x] Switch test/extension/dynamic_image  
- [x] Switch test/extension/io planned as separate task in #461 after this PR is merged (done in #464)  
- [x] Switch test/extension/numeric  
- [x] Switch test/extension/toolbox  
- [x] Switch test/legacy  
- [x] Ensure all CI builds pass  
- [x] Update documentation (may be done as separate PRs)  
    - [x] Update `README.md`  
    - [x] Update `CONTRIBUTING.md`  
    - [x] Check if all other docs need update

---
