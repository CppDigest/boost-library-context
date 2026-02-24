# #566 - Add codecov badge to readme and correct badge sequence [Closed]

> Username: avinal  
> Created at: 2021-02-28 09:14:18 UTC  
> Updated at: 2021-03-01 10:46:27 UTC  
> Closed at: 2021-03-01 10:46:27 UTC  
> Url: https://github.com/boostorg/gil/issues/566  

### Actual behavior  
  
AppVeyor is missing from the badges.   
![image](https://user-images.githubusercontent.com/74113200/109413277-94b64b80-79d2-11eb-950b-8dba78b1d573.png)  
  
### Expected  behavior  
  
Documentation | GitHub Actions | AppVeyor | Azure Pipelines | CircleCI        | Regression  
--------------|----------------|-----------------|-----------------|------------|-------------  
[![develop](https://img.shields.io/badge/doc-develop-blue.svg)](https://boostorg.github.io/gil/develop/) | [![GitHub Actions](https://github.com/boostorg/gil/workflows/CI/badge.svg?branch=develop)](https://github.com/boostorg/gil/actions?query=branch:develop) | [![AppVeyor](https://ci.appveyor.com/api/projects/status/w4k19d8io2af168h/branch/develop?svg=true)](https://ci.appveyor.com/project/stefanseefeld/gil/branch/develop) | [![Azure](https://dev.azure.com/boostorg/gil/_apis/build/status/boostorg.gil?branchName=develop)](https://dev.azure.com/boostorg/gil/_build/latest?definitionId=7&branchName=develop) | [![CircleCI](https://circleci.com/gh/boostorg/gil/tree/develop.svg?style=shield)](https://circleci.com/gh/boostorg/workflows/gil/tree/develop) | [![gil](https://img.shields.io/badge/gil-develop-blue.svg)](http://www.boost.org/development/tests/develop/developer/gil.html)  
[![master](https://img.shields.io/badge/doc-master-blue.svg)](https://boostorg.github.io/gil/) | [![GitHub Actions](https://github.com/boostorg/gil/workflows/CI/badge.svg?branch=master)](https://github.com/boostorg/gil/actions?query=branch:master) | [![AppVeyor](https://ci.appveyor.com/api/projects/status/w4k19d8io2af168h?svg=true)](https://ci.appveyor.com/project/stefanseefeld/gil/branch/master) | [![Azure](https://dev.azure.com/boostorg/gil/_apis/build/status/boostorg.gil?branchName=master)](https://dev.azure.com/boostorg/gil/_build/latest?definitionId=7&branchName=master) | [![CircleCI](https://circleci.com/gh/boostorg/gil/tree/master.svg?style=shield)](https://circleci.com/gh/boostorg/workflows/gil/tree/master) | [![gil](https://img.shields.io/badge/gil-master-blue.svg)](http://www.boost.org/development/tests/master/developer/gil.html)  
  
- Add Codecov config and badges
