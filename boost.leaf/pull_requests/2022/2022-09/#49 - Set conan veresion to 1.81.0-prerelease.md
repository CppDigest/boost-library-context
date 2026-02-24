# #49 Set conan veresion to 1.81.0-prerelease [Merged]

> Username: kammce  
> Created at: 2022-09-08 13:37:56 UTC  
> Updated at: 2022-09-09 04:31:33 UTC  
> Merged at: 2022-09-09 02:14:05 UTC  
> Closed at: 2022-09-09 02:14:05 UTC  
> Url: https://github.com/boostorg/leaf/pull/49  

This version is the prerelease version of the offical 1.81.0 boost versino. In semver, version with tags have lower precedence over unlabled versions. To use this version in a conanfile the version range will have to look like this:  
  
    boost-leaf/[>1.80.0, include_prerelease=True]  
  
or to simply get the latest  
  
    boost-leaf/[x, include_prerelease=True]

---
