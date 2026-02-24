# #96 Bring backmp11 backend upstream [Merged]

> Username: chandryan  
> Created at: 2025-08-03 15:59:35 UTC  
> Updated at: 2025-09-28 04:02:13 UTC  
> Merged at: 2025-09-07 14:27:12 UTC  
> Closed at: 2025-09-07 14:27:12 UTC  
> Url: https://github.com/boostorg/msm/pull/96  

**SCOPE:**  
  
Introduce the backmp11 backend in the upstream MSM repository.  
  
Related to https://github.com/boostorg/msm/issues/82.  
  
**DONE:**  
  
- Implemented a C++11-optimized backend `backmp11` that utilizes mp11 (benchmarks are available [here](https://github.com/chandryan/fsm-benchmarks#benchmark-results))  
- Refactored tests to test all available backends and compile time policies  
- Ensured C++11 compatibility of backmp11 and absence of any pedantic compiler warnings  
- Updated Jamfiles to reflect new tests and mp11 dependency  
- Ran the tests from the Boost superproject  
  
  
**OPEN POINTS:**  
  
- Documentation for backmp11 is still missing, I was thinking to mention similar information as is currently available in `include/boost/msm/backmp11/README.md`. I could add it in this PR or later, though I need some guidance how to build it properly

---
