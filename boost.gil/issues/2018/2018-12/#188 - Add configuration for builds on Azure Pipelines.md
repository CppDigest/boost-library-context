# #188 - Add configuration for builds on Azure Pipelines [Closed]

> Username: mloskot  
> Created at: 2018-12-12 16:55:24 UTC  
> Updated at: 2019-01-04 21:15:51 UTC  
> Closed at: 2019-01-04 21:15:51 UTC  
> Url: https://github.com/boostorg/gil/issues/188  

## Current CI builds  
  
Currently, we have CI builds configured to run minimal core and extensions tests   
  
```  
libs/gil/test  
libs/gil/toolbox/test  
libs/gil/numeric/test  
libs/gil/io/test//simple  
```  
  
on  
  
- [.travis.yml](https://github.com/boostorg/gil/blob/develop/.travis.yml) - Linux using GCC 5, 6, 7 and clang 3.9, 4.0  
- [.appveyor.yml](https://github.com/boostorg/gil/blob/develop/.appveyor.yml) - on Windows using VS2017  
- [.circleci/config.yml](https://github.com/boostorg/gil/blob/develop/.circleci/config.yml) - on Linux using _~20 versions_ of GCC 4.8-8.2 and clang 3.9-5.0  
  
for `variant=debug,release`, `address-model=64` only and  `C++11` compilation mode only.  
  
The three CI services are already _heavily occupied_.  
  
## More CI builds needed  
  
Due to the limited resources on currently used CI services, we do not run  
  
- `libs/gil/io/test//full` which extensively covers the IO implementation  
- headers tests (self-contained, all-in-one)  
- linting analysis  (e.g. clang-tidy, see https://github.com/boostorg/gil/commit/aff86c252ce7452899a8e0b6117a683b8b27b093).  
  
In future, we also may split the long-lived monolithic tests into smaller suites, for better coverage, clearer reports and easier maintenance. That, will likely increase compilation times.  So, more resources for CI builds will be required.  
  
## Azure Pipelines  
  
Basic steps to set the Pipelines for GIL:  
  
1. Add Azure Pipelines from https://github.com/marketplace/azure-pipelines  
2. Create `azure-pipelines.yml` (see [Azure Pipelines YAML](https://docs.microsoft.com/en-us/azure/devops/pipelines/yaml-schema?view=vsts&tabs=schema)).  
3. Decide  
  
  - which targets/tests to build  
  - how to build (variants, architectures)  
  - how to configure (e.g. it would be nice to have equivalent builds for all Boost.Build, CMake, faber)  
  - on what OS-es/images  
  
4. Design the pipelines, jobs and steps accordingly.  
  
Help wanted!  
  
## References  
  
- https://lists.boost.org/boost-gil/2018/12/0122.php

---

## Comment 1

> Username: mloskot  
> Created at: 2018-12-18 23:50:21 UTC  
> Url: https://github.com/boostorg/gil/issues/188#issuecomment-448415578  

https://github.com/boostorg/gil/commit/42824b1f37b6c9912c4b9ead7a1834a567d9f63b  
```  
First stab at CI with Azure Pipelines [skip appveyor] [skip travis]  
  
Add sample config file, no-op build.  
```

---

## Comment 2

> Username: mloskot  
> Created at: 2018-12-19 00:19:24 UTC  
> Updated at: 2018-12-19 00:20:14 UTC  
> Url: https://github.com/boostorg/gil/issues/188#issuecomment-448421758  

All AP experiments moved to dedicated [azure-pipelines](https://github.com/boostorg/gil/tree/azure-pipelines) branch.
