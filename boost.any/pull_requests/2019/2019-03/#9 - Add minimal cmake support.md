# #9 [CMake] Add minimal cmake support [Merged]

> Username: Mike-Devel  
> Created at: 2019-03-08 10:28:53 UTC  
> Updated at: 2019-03-09 22:36:11 UTC  
> Merged at: 2019-03-09 18:58:34 UTC  
> Closed at: 2019-03-09 18:58:34 UTC  
> Url: https://github.com/boostorg/any/pull/9  

This cmake file just provides the minimal infrastructure necessary, such that other libraries can get a sensible result from calling  
  
  
    add_subdirectory( <path-to-boost_any> )  
    target_link_libraries( <my_lib> PUBLIC Boost::any )  
  
  
That assumes that all direct and indirect dependencies are also being added via `add_subdirectory` (which can e.g. happen via globbing expressions).  
  
Some background information:  
  
* https://groups.google.com/forum/#!topic/boost-developers-archive/9ZdrVbAn1aU  
  
  
Of course the file can be extended to e.g. build and run tests and support installation, but that is out of scope for this particular PR.  
  
Please note that this is largely orthogonal to the recent addition to b2/boost by peter dimov, which is concerned with providing cmake config files for "classic" boost installations via b2.

---

## Comment 1

> Username: apolukhin  
> Created_at: 2019-03-09 18:58:44 UTC  
> Url: https://github.com/boostorg/any/pull/9#issuecomment-471211773  

Many thanks!

---
