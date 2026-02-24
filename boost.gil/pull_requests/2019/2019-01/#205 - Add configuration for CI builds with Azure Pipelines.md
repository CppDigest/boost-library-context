# #205 Add configuration for CI builds with Azure Pipelines [Merged]

> Username: mloskot  
> Created at: 2019-01-04 20:50:44 UTC  
> Updated at: 2019-01-04 21:15:56 UTC  
> Merged at: 2019-01-04 21:15:51 UTC  
> Closed at: 2019-01-04 21:15:52 UTC  
> Url: https://github.com/boostorg/gil/pull/205  

Configuration summary:  
  - Linux (GCC 5.4), Windows (VS2017), Mac OS (clang 4.0)  
  - Boost 1.68 built with variant=release  
  - Boost.GIL built and tested using CMake w/ CMAKE_BUILD_TYPE=Release  
  - Basic setup of GIL IO extension dependencies is in place,  
    using .deb packages or Conan.  
  - Build without GIL IO extension tests due to issues in CMake  
    configuration, not related to Azure Pipelines.  
  
### References  
  
- Closes #188  
  
### Tasklist  
  
- [x] Azure Pipelines builds and checks have passed (Travis and AppVeyor skipped)

---
