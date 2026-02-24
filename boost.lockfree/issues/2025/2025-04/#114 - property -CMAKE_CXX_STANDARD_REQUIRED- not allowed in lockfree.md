# #114 - property "CMAKE_CXX_STANDARD_REQUIRED" not allowed in lockfree [Closed]

> Username: drok  
> Created at: 2025-04-03 20:39:22 UTC  
> Updated at: 2025-04-05 01:59:36 UTC  
> Closed at: 2025-04-05 01:59:36 UTC  
> Url: https://github.com/boostorg/lockfree/issues/114  

I think this is a trivial failure somewhere, but seems to require expertise in the boost build process, which I don't have. I attempted to start a codespace at github on master (199ef13d6034c8), and to simply build the library with the default settings, as a dry-run before attempting to take on this beast.  
  
My initial goal is to run the test suite of `boost::unordered_flat_map`, and ultimately to use that module in a project, and no other boost stuffs. I'd like to make changes to the unordered implementation, and be able to re-run the shipping test suite.  
  
Embarking on this quest, I wanted to first duplicate the CI `cmake-install-posix (ubuntu-20.04)` job ([this one, specifically](https://github.com/boostorg/boost/actions/runs/14241933622/job/39913632134) on github's codespace, but failed the configuration step with the error below.  
  
I'm not familiar with boost at all, but I expected building with the official instructions to complete without errors.  
  
```  
CMake Error at libs/lockfree/CMakeLists.txt:45 (set_target_properties):  
  INTERFACE_LIBRARY targets may only have whitelisted properties.  The  
  property "CMAKE_CXX_STANDARD_REQUIRED" is not allowed.  
```  
  
This failure happens on the very first step:  
  
```  
mkdir __build__ && cd __build__  
cmake -DBUILD_SHARED_LIBS=ON -DCMAKE_INSTALL_PREFIX=~/.local ..  
```  
  
### What has gone wrong here?  
  
Thank you

---

## Comment 1

> Username: pdimov  
> Created at: 2025-04-04 16:40:53 UTC  
> Url: https://github.com/boostorg/lockfree/issues/114#issuecomment-2779260148  

Looks like a problem in Lockfree to me.
