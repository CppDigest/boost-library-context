# #50 [CMake] Enable running (some) unit tests from cmake [Closed]

> Username: Mike-Devel  
> Created at: 2019-05-01 09:36:19 UTC  
> Updated at: 2019-12-08 13:14:22 UTC  
> Closed at: 2019-12-08 13:14:22 UTC  
> Url: https://github.com/boostorg/iterator/pull/50  

This requires   
  
1) Boost.iterator to be checked out as prat of the Boost Superproject, with all the necessary dependencies.  
2) A file like this being added to the boost root directory : https://github.com/boostorg/boost/pull/193/files  
  Important aspects:  
   - Top level cmake file needs to call `add_subdirectory( <path-to-lib> )` on at least all direct and indirect dependencies of Boost.Iterator and on Boost.Iterator itself  
   - Must call enable_testing() before `add_subdirectory`  
  
To build and run the tests:  
  
 - `mkdir __build__ && cd __build__`  
 - `cmake -DBOOST_ITERATOR_INCLUDE_TESTS=ON <path-to-boost-root>`  
 - `ctest .`  
  
Note: some of the unit tests are currently ignored for various resons documented in the file.

---

## Comment 1

> Username: Lastique  
> Created_at: 2019-05-01 11:12:26 UTC  
> Url: https://github.com/boostorg/iterator/pull/50#issuecomment-488258634  

I think it needs to run all tests. If it can't be done then I'd rather not bother with CMake, not for testing anyway.

---

## Comment 2

> Username: Mike-Devel  
> Created_at: 2019-05-01 11:32:19 UTC  
> Url: https://github.com/boostorg/iterator/pull/50#issuecomment-488261640  

Sure. In that case I'll extend the PR, once the remaining test dependencies support cmake

---

## Comment 3

> Username: Mike-Devel  
> Created_at: 2019-12-08 13:14:22 UTC  
> Url: https://github.com/boostorg/iterator/pull/50#issuecomment-562947120  

closing this for now

---
