# #61 [CMake] Add minimal cmake support [Merged]

> Username: Mike-Devel  
> Created at: 2019-03-08 15:29:57 UTC  
> Updated at: 2019-03-09 22:35:51 UTC  
> Merged at: 2019-03-09 18:57:31 UTC  
> Closed at: 2019-03-09 18:57:31 UTC  
> Url: https://github.com/boostorg/variant/pull/61  

his cmake file just provides the minimal infrastructure necessary, such that other libraries can get a sensible result from calling  
  
  
    add_subdirectory( <path-to-boost_variant> )  
    target_link_libraries( <my_lib> PUBLIC Boost::variant )  
  
  
That assumes that all direct and indirect dependencies are also being added via `add_subdirectory` (which can e.g. happen via globbing expressions).  
  
Some background information:  
  
  * https://groups.google.com/forum/#!topic/boost-developers-archive/9ZdrVbAn1aU  
  
  
Of course the file can be extended to e.g. build and run tests and support installation, but that is out of scope for this particular PR.  
  
Please note that this is largely orthogonal to the recent addition to b2/boost by peter dimov, which is concerned with providing cmake config files for "classic" boost installations via b2.

---

## Comment 1

> Username: coveralls  
> Created_at: 2019-03-08 15:55:50 UTC  
> Updated_at: 2019-03-08 19:10:53 UTC  
> Url: https://github.com/boostorg/variant/pull/61#issuecomment-470977289  

[![Coverage Status](https://coveralls.io/builds/22072784/badge)](https://coveralls.io/builds/22072784)  
  
Coverage remained the same at 95.063% when pulling **98e2674467b05355ec20d76c6531e6013a5b5bb3 on Mike-Devel:min_cmake** into **d069511e31a3b7fee45e22634bb43b8f4e4608cd on boostorg:develop**.

---

## Comment 2

> Username: apolukhin  
> Created_at: 2019-03-09 18:57:39 UTC  
> Url: https://github.com/boostorg/variant/pull/61#issuecomment-471211677  

Thanks!

---
