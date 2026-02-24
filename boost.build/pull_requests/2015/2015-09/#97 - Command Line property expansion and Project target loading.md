# #97 Command Line property expansion and Project target loading [Closed]

> Username: frenchtoast747  
> Created at: 2015-09-23 23:41:31 UTC  
> Updated at: 2021-10-02 22:20:15 UTC  
> Closed at: 2015-10-13 06:59:52 UTC  
> Url: https://github.com/boostorg/build/pull/97  

**tl;dr** have a look at the included test to see the scenario.  
  
Assume there are three jamfiles: `Jamroot.jam`, `subdir/build.jam`, and `subdir/subsubdir/build.jam` where each file is a parent to the following.   
  
Assume that I have a feature declared in `subdir/build.jam`:  
  
```  
import feature ;  
feature.feature my-feature : : free ;  
```  
  
And assume that I have some target to build in the grandchild jamfile, `subdir/subsubdir/build.jam`  
  
```  
exe hello : hello.c ;  
```  
  
If I try building that target from the root while also specifying the feature declared in `subdir/build.jam`, I will get a feature validation error:  
  
```  
$ b2 subdir/subsubdir my-feature="some free value"  
error: unknown feature "<my-feature>"  
C:/boost-build/src/share/boost-build/src/build\feature.jam:359: in expand-subfeatures-aux from module feature  
C:/boost-build/src/share/boost-build/src/build\feature.jam:424: in feature.expand-subfeatures from module feature  
C:/boost-build/src/share/boost-build/src/build\build-request.jam:20: in apply-to-property-set from module build-request  
(builtin):-1: in sequence.transform from module sequence  
C:/boost-build/src/share/boost-build/src/build\build-request.jam:32: in build-request.expand-no-defaults from  
module build-request  
C:/boost-build/src/share/boost-build/src\build-system.jam:594: in load from module build-system  
C:/boost-build\src\share\boost-build\src\kernel\modules.jam:289: in import from module modules  
C:/boost-build\src\share\boost-build\src\kernel\bootstrap.jam:139: in boost-build from module  
C:/boost-build\src\share\boost-build\boost-build.jam:8: in module scope from module  
```  
  
This change delays the property validation and expansion as late as possible to ensure that all projects/modules have been loaded.

---

## Comment 1

> Username: vprus  
> Created_at: 2015-10-11 17:05:33 UTC  
> Url: https://github.com/boostorg/build/pull/97#issuecomment-147221496  

Aaron,  
  
thanks for the patch, I think it's overall good. It appears to make unit_tests.py fail - is this something you can fix?

---

## Comment 2

> Username: frenchtoast747  
> Created_at: 2015-10-11 17:42:05 UTC  
> Url: https://github.com/boostorg/build/pull/97#issuecomment-147224368  

Yes, I will fix the unit tests for build-request.jam and push a fix soon. Thanks!

---

## Comment 3

> Username: frenchtoast747  
> Created_at: 2015-10-13 02:46:18 UTC  
> Url: https://github.com/boostorg/build/pull/97#issuecomment-147579806  

Since the functionality of `from-command-line` was split, I've updated the unit tests to reflect that.

---

## Comment 4

> Username: vprus  
> Created_at: 2015-10-13 06:59:46 UTC  
> Url: https://github.com/boostorg/build/pull/97#issuecomment-147624742  

Thanks, cherry-picked to develop.

---

## Comment 5

> Username: frenchtoast747  
> Created_at: 2015-10-13 15:45:21 UTC  
> Url: https://github.com/boostorg/build/pull/97#issuecomment-147755603  

Thank you!

---
