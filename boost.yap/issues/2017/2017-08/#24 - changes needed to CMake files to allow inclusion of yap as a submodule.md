# #24 - changes needed to CMake files to allow inclusion of yap as a submodule [Closed]

> Username: rgrover  
> Created at: 2017-08-13 07:39:53 UTC  
> Updated at: 2018-06-11 17:16:18 UTC  
> Closed at: 2017-08-16 20:19:58 UTC  
> Url: https://github.com/boostorg/yap/issues/24  

CMake files within yap make use of variables such as `${CMAKE_SOURCE_DIR}` which make it difficult to include yap as a sub-module of an external project. Use of `${CMAKE_CURRENT_SOURCE_DIR}` would be more appropriate.
