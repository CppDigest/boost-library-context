# #16 [CMake] Add minimal cmake support [Merged]

> Username: Mike-Devel  
> Created at: 2019-03-03 08:43:37 UTC  
> Updated at: 2019-03-04 08:00:33 UTC  
> Merged at: 2019-03-03 18:55:00 UTC  
> Closed at: 2019-03-03 18:55:00 UTC  
> Url: https://github.com/boostorg/conversion/pull/16  

This cmake file just provides the minimal infrastructure necessary, such that other libraries can get a sensible result from calling  
  
  
    add_subdirectory( <path-to-boost_conversion> )  
    target_link_libraries( <my_lib> PUBLIC Boost::conversion )  
  
  
provided that all direct and indirect dependencies are also being added via `add_subdirectory` (which can e.g happen via globbing expression).  
  
More information:  
  
    * https://groups.google.com/forum/#!topic/boost-developers-archive/9ZdrVbAn1aU  
  
  
Of course the file can be extended to e.g. build and run tests and support installation, but that is out of scope for this particular PR.  
  
Please note that this is largely orthogonal to the recent addition to b2/boost by peter dimov, which is concerned with providing cmake config files for "classic" boost installations via b2.

---

## Comment 1

> Username: apolukhin  
> Created_at: 2019-03-03 18:55:18 UTC  
> Url: https://github.com/boostorg/conversion/pull/16#issuecomment-469052323  

Thanks for the PR!

---

## Comment 2

> Username: Mike-Devel  
> Created_at: 2019-03-04 08:00:29 UTC  
> Url: https://github.com/boostorg/conversion/pull/16#issuecomment-469154077  

Thanks for the quick merge!

---
