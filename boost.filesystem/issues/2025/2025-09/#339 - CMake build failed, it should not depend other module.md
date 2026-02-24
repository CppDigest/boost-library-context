# #339 - CMake build failed, it should not depend other module [Closed]

> Username: wangguodong-f  
> Created at: 2025-09-12 11:34:11 UTC  
> Updated at: 2025-09-12 11:44:29 UTC  
> Closed at: 2025-09-12 11:44:29 UTC  
> Url: https://github.com/boostorg/filesystem/issues/339  

-- Configuring done (0.0s)  
CMake Error at CMakeLists.txt:228 (target_link_libraries):  
  Target "boost_filesystem" links to:  
  
    Boost::assert  
  
  but the target was not found.  Possible reasons include:  
  
    * There is a typo in the target name.  
    * A find_package call is missing for an IMPORTED target.  
    * An ALIAS target is missing.  
  
  
  
-- Generating done (0.0s)  
CMake Generate step failed.  Build files cannot be regenerated correctly.

---

## Comment 1

> Username: Lastique  
> Created at: 2025-09-12 11:44:29 UTC  
> Url: https://github.com/boostorg/filesystem/issues/339#issuecomment-3284953507  

You probably did not checkout Boost.Filesystem dependencies, specifically [Boost.Assert](https://github.com/boostorg/assert). You can use [depinst.py](https://github.com/boostorg/boostdep/blob/develop/depinst/depinst.py) to automatically checkout all dependencies of a Boost library, see how it's done in [CI](https://github.com/boostorg/filesystem/blob/ce7a835436f5de0cca2966420068a2596f50a75d/.github/workflows/ci.yml#L504).
