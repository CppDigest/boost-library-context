# #92 Test Boost configure with different CMake versions [Merged]

> Username: Flamefire  
> Created at: 2025-10-01 14:26:33 UTC  
> Updated at: 2025-10-02 06:47:31 UTC  
> Merged at: 2025-10-01 19:32:10 UTC  
> Closed at: 2025-10-01 19:32:10 UTC  
> Url: https://github.com/boostorg/cmake/pull/92  

As [previously discussed](https://github.com/boostorg/cmake/pull/90#issuecomment-3344070496) this adds CI jobs that configure the Boost tree with various CMake versions.  
  
For that the CMake source is downloaded and compiled, then cached for future jobs.     
Using existing binary downloads failed due to e.g. updated OpenSSL libraries installed on the system. For a similar reason libcurl-dev is installed to be used by CMake instead of it building its bundled version of cURL which is incompatible with the system OpenSSL in some CMake versions.  
  
  
Individual Boost libraries require different minimal CMake versions. That should be declared in their CMakeLists correctly. The `cmake_minimum_required` line is used to determine which libraries to exclude for the currently tested CMake version.     
Some depend on libraries requiring a higher CMake version than itself or fail to declare their required CMake version, hence a manual list is maintained in addition to that.  
  
The list of tested CMake versions is based on features selected depending on the CMake version:  
- 3.8 as the minimal required one, see https://github.com/boostorg/boost/pull/1088  
- 3.9 as the one where BoostTestJamfile works  
- 3.10 checked for by BoostInstall.cmake  
- 3.13 As the first to support automatic installation via BoostInstall  
- 3.31 as the last 3.x version  
- 4.0 as the first 4.x version  
- 4.1 as the current version  
- Some versions inbetween based on usage by Boost libraries and known prior failures (e.g. 3.11 allows whitelisted properties on INTERFACE libraries, others fail as seen with 3.16 on Windows, 3.19 lifted the restriction)

---

## Comment 1

> Username: pdimov  
> Created_at: 2025-10-01 14:55:01 UTC  
> Url: https://github.com/boostorg/cmake/pull/92#issuecomment-3356832023  

```  
          # Libraries that don't declare their correct minimal required CMake version or where dependencies require higher version  
          version_greater_equal ${{matrix.cmake_version}} 3.10.0 || excluded+=("gil")  
          version_greater_equal ${{matrix.cmake_version}} 3.12.0 || excluded+=("msm")  
          version_greater_equal ${{matrix.cmake_version}} 3.14.0 || excluded+=("pfr" "mysql")  # mysql depends on pfr  
```  
Should we do something about these?

---

## Comment 2

> Username: Flamefire  
> Created_at: 2025-10-01 15:05:58 UTC  
> Updated_at: 2025-10-01 15:06:10 UTC  
> Url: https://github.com/boostorg/cmake/pull/92#issuecomment-3356880049  

- [ ] msm: https://github.com/boostorg/msm/pull/122  
- [ ] https://github.com/boostorg/pfr/pull/223  
- gil was a mistake based on a 2nd `cmake_minimum_required` used for standalone builds -- removed  
- mysql has a dependency on pfr but doesn't require 3.14 itself. Once the pfr PR is merged both exceptions can be removed (pfr actually requires only 3.5, maybe 3.8)

---
