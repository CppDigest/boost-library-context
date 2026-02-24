# #40 [CMake] Add minimal cmake support [Merged]

> Username: Mike-Devel  
> Created at: 2019-03-02 15:59:02 UTC  
> Updated at: 2019-03-04 16:13:37 UTC  
> Merged at: 2019-03-04 14:59:24 UTC  
> Closed at: 2019-03-04 14:59:24 UTC  
> Url: https://github.com/boostorg/intrusive/pull/40  

This cmake file just provides the minimal infrastructure necessary, such that other libraries can get a sensible result from calling  
  
  
    add_subdirectory( <path-to-boost_intrusive> )  
    target_link_libraries( <my_lib> PUBLIC Boost::intrusive )  
  
  
provided that all direct and indirect dependencies are also being added via `add_subdirectory` (which can e.g happen via globbing expression).  
  
More information:  
  
    * https://groups.google.com/forum/#!topic/boost-developers-archive/9ZdrVbAn1aU  
  
  
Of course the file can be extended to e.g. build and run tests and support installation, but that is out of scope for this particular PR.  
  
Please note that this is largely orthogonal to the recent addition to b2/boost by peter dimov, which is concerned with providing cmake config files for "classic" boost installations via b2.

---

## Comment 1

> Username: igaztanaga  
> Created_at: 2019-03-04 14:59:36 UTC  
> Url: https://github.com/boostorg/intrusive/pull/40#issuecomment-469283092  

Many thanks for the patch.

---
