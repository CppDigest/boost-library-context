# #27 [CMake] Add minimal cmake file [Merged]

> Username: Mike-Devel  
> Created at: 2019-02-14 17:04:49 UTC  
> Updated at: 2019-02-21 08:13:58 UTC  
> Merged at: 2019-02-21 07:27:24 UTC  
> Closed at: 2019-02-21 07:27:24 UTC  
> Url: https://github.com/boostorg/type_index/pull/27  

This cmake file just provides the minimal infrastructure necessary, such that other libraries can get a sensible result from calling  
  
```  
add_subdirectory( <path-to-boost_type_index> )  
target_link_libraries( <my_lib> PUBLIC Boost::type_index )  
```  
provided that all direct and indirect dependencies are also being added via `add_subdirectory` (which can e.g happen via globbing expression).  
  
More information:  
  
* https://groups.google.com/forum/#!topic/boost-developers-archive/9ZdrVbAn1aU  
  
Of course the file can be extended to e.g. build and run tests and support installation, but that is out of scope for this particular PR.  
  
Please note that this is largely orthogonal to the recent addition to b2/boost by peter dimov, which is concerned with providing cmake config files for "classic" boost installations via b2.

---
