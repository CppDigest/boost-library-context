# #804 - Boost CMake testing procedure doesn't work for URL [Closed]

> Username: pdimov  
> Created at: 2023-12-17 00:42:53 UTC  
> Updated at: 2023-12-22 15:05:57 UTC  
> Closed at: 2023-12-22 15:05:56 UTC  
> Url: https://github.com/boostorg/url/issues/804  

The [documented testing procedure](https://github.com/boostorg/cmake#testing-boost-with-cmake)  
  
```  
mkdir __build && cd __build  
cmake -DBUILD_TESTING=ON -DBOOST_INCLUDE_LIBRARIES=url ..  
cmake --build . --target tests  
ctest --output-on-failure --no-tests=error  
```  
  
fails for Boost.URL, because the tests aren't built by the target `tests`. This target is declared (twice, slightly differently) in test/CMakeLists.txt  
  
https://github.com/boostorg/url/blob/b8b91407fdea3d63517786cd174929ed59138595/test/CMakeLists.txt#L11-L15  
  
and test/unit/CMakeList.txt  
  
https://github.com/boostorg/url/blob/b8b91407fdea3d63517786cd174929ed59138595/test/unit/CMakeLists.txt#L11-L15  
  
but then isn't used. It's possible that this used to work and then was lost in a refactoring, because the tests are made dependencies of `boost_url_all_tests` instead. The intent here might have been to make `boost_url_all_tests` a dependency of `tests`, but this hasn't been done.  
  
(For this to keep working after the eventual fix, it might be a good idea to add it to CI.)  
  
It's also good practice to make the test executables EXCLUDE_FOR_ALL, so that building the `tests` target builds them, but building the default target doesn't.
