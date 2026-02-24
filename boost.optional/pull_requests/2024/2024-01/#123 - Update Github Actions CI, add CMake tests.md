# #123 Update Github Actions CI, add CMake tests [Merged]

> Username: pdimov  
> Created at: 2024-01-15 18:52:07 UTC  
> Updated at: 2024-01-15 22:48:50 UTC  
> Merged at: 2024-01-15 19:11:53 UTC  
> Closed at: 2024-01-15 19:11:53 UTC  
> Url: https://github.com/boostorg/optional/pull/123  

This pull request  
  
* updates GHA CI by copying ci.yml from SmartPtr  
* fixes an error under C++03 caused by Bind no longer supporting it  
* regenerates CMakeLists.txt using `boostdep --cmake optional`  
* adds test/CMakeLists.txt so that tests can be run under CMake  
* simplifies test/Jamfile.v2 so that the CMake function BoostTestJamfile can parse it  
* adds subdirectory and install CMake tests

---

## Comment 1

> Username: akrzemi1  
> Created_at: 2024-01-15 20:45:39 UTC  
> Url: https://github.com/boostorg/optional/pull/123#issuecomment-1892746839  

I get a lot of fails from CI:  
https://github.com/boostorg/optional/actions/runs/7533028201/  
I also got them before the PR.  
They do not seem to be caused by test cases from Optional, but rather from the testing environment.  
Maybe you know the cause?

---

## Comment 2

> Username: pdimov  
> Created_at: 2024-01-15 22:48:49 UTC  
> Url: https://github.com/boostorg/optional/pull/123#issuecomment-1892843726  

The new GHA CI is all green.

---
