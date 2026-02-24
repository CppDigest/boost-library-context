# #106 [CMake] Add minimal cmake support [Merged]

> Username: Mike-Devel  
> Created at: 2019-03-06 14:49:46 UTC  
> Updated at: 2019-03-07 05:23:34 UTC  
> Merged at: 2019-03-06 22:37:23 UTC  
> Closed at: 2019-03-06 22:37:23 UTC  
> Url: https://github.com/boostorg/container/pull/106  

This cmake file just provides the minimal infrastructure necessary, such that other libraries can get a sensible result from calling  
  
  
    add_subdirectory( <path-to-boost_container> )  
    target_link_libraries( <my_target> PUBLIC Boost::container )  
  
  
provided that all direct and indirect dependencies are also being added via `add_subdirectory` (which can e.g happen via globbing expression).  
  
More information:  
  
    * https://groups.google.com/forum/#!topic/boost-developers-archive/9ZdrVbAn1aU  
  
  
Of course the file can be extended to e.g. build and run tests and support installation, but that is out of scope for this particular PR.  
  
Please note that this is largely orthogonal to the recent addition to b2/boost by peter dimov, which is concerned with providing cmake config files for "classic" boost installations via b2.

---

## Comment 1

> Username: igaztanaga  
> Created_at: 2019-03-06 22:37:37 UTC  
> Url: https://github.com/boostorg/container/pull/106#issuecomment-470305698  

Many thanks for the patch.

---
