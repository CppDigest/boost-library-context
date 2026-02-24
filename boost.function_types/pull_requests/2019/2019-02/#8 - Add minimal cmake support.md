# #8 [CMake] Add minimal cmake support [Merged]

> Username: Mike-Devel  
> Created at: 2019-02-14 17:58:14 UTC  
> Updated at: 2019-03-04 21:47:58 UTC  
> Merged at: 2019-03-04 21:47:15 UTC  
> Closed at: 2019-03-04 21:47:15 UTC  
> Url: https://github.com/boostorg/function_types/pull/8  

This cmake file just provides the minimal infrastructure necessary, such that other libraries can get a sensible result from calling  
  
  
    add_subdirectory( <path-to-boost_function_types> )  
    target_link_libraries( <my_lib> PUBLIC Boost::function_types )  
  
  
provided that all direct and indirect dependencies are also being added via `add_subdirectory` (which can e.g happen via globbing expression).  
  
More information:  
  
* https://groups.google.com/forum/#!topic/boost-developers-archive/9ZdrVbAn1aU  
  
  
Of course the file can be extended to e.g. build and run tests and support installation, but that is out of scope for this particular PR.  
  
Please note that this is largely orthogonal to the recent addition to b2/boost by peter dimov, which is concerned with providing cmake config files for "classic" boost installations via b2.

---

## Comment 1

> Username: Mike-Devel  
> Created_at: 2019-02-23 10:53:36 UTC  
> Url: https://github.com/boostorg/function_types/pull/8#issuecomment-466638301  

Should I add tests for this? I lost traock of which library maintainer wanted them and who didn't.

---

## Comment 2

> Username: Mike-Devel  
> Created_at: 2019-03-04 21:47:56 UTC  
> Url: https://github.com/boostorg/function_types/pull/8#issuecomment-469434811  

Thanks

---
