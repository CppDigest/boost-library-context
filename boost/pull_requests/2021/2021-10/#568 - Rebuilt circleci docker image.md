# #568 Rebuilt circleci docker image [Merged]

> Username: sdarwin  
> Created at: 2021-10-15 14:24:04 UTC  
> Updated at: 2021-10-18 15:20:15 UTC  
> Merged at: 2021-10-18 15:20:14 UTC  
> Closed at: 2021-10-18 15:20:15 UTC  
> Url: https://github.com/boostorg/boost/pull/568  

Boost archives are not being published to jfrog because of the recent Let's Encrypt problem.    
  
Rebuilding the docker images solves the issue.    
  
Dockerfiles: https://github.com/boostorg/release-tools/pull/18/files  
  
Still python2, instead of python3.  
  
cppalliance/boost_superproject_build:build-deps-3 based on Ubuntu 16.04  
cppalliance/boost_superproject_build:build-deps-4 based on Ubuntu 18.04  
  
Organizationally, it would make sense to host the docker images at boostorg/ instead of cppalliance/ .    You are welcome to make that change: Rebuild with "docker build".   Upload to boostorg/ .

---
