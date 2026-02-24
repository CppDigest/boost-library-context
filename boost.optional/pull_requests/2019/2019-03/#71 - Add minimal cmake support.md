# #71 [CMake] Add minimal cmake support [Merged]

> Username: Mike-Devel  
> Created at: 2019-03-02 16:13:57 UTC  
> Updated at: 2019-03-04 10:14:05 UTC  
> Merged at: 2019-03-04 09:43:46 UTC  
> Closed at: 2019-03-04 09:43:46 UTC  
> Url: https://github.com/boostorg/optional/pull/71  

This cmake file just provides the minimal infrastructure necessary, such that other libraries can get a sensible result from calling  
  
  
    add_subdirectory( <path-to-boost_optional> )  
    target_link_libraries( <my_lib> PUBLIC Boost::optional )  
  
  
provided that all direct and indirect dependencies are also being added via `add_subdirectory` (which can e.g happen via globbing expression).  
  
More information:  
  
    * https://groups.google.com/forum/#!topic/boost-developers-archive/9ZdrVbAn1aU  
  
  
Of course the file can be extended to e.g. build and run tests and support installation, but that is out of scope for this particular PR.  
  
Please note that this is largely orthogonal to the recent addition to b2/boost by peter dimov, which is concerned with providing cmake config files for "classic" boost installations via b2.

---

## Comment 1

> Username: akrzemi1  
> Created_at: 2019-03-04 09:45:40 UTC  
> Url: https://github.com/boostorg/optional/pull/71#issuecomment-469186470  

Thanks.

---

## Comment 2

> Username: Mike-Devel  
> Created_at: 2019-03-04 10:14:05 UTC  
> Url: https://github.com/boostorg/optional/pull/71#issuecomment-469196457  

Thanks for the quick merge

---
