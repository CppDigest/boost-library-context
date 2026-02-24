# #167 - Define CMake target Boost::gil in CMakeLists.txt [Open]

> Username: Mike-Devel  
> Created at: 2018-11-12 12:05:54 UTC  
> Updated at: 2022-12-15 07:03:16 UTC  
> Url: https://github.com/boostorg/gil/issues/167  

Could you please add a (interface library) cmake target for gil, that specifies the include directory and dependencies.  
  
E.g something like:  
  
    add_library(boost_gil INTERFACE)  
    target_include_directories(boost_gil INTERFACE include)    
    target_compile_options(boost_gil INTERFACE include ...)   
    add_library(Boost::gil ALIAS boost_gil)  
  
    target_link_libraries(boost_gil INTERFACE  
      Boost::filesystem  
      Boost::boost  
      JPEG::JPEG  
      PNG::PNG  
      TIFF::TIFF  
    )  
  
Cmake targets depending on Boost.Gil (like your examples and test, or a user progam doing a `add_subdirectory(libs/gil)`) could then just use `target_link_libraries( my_exec Boost::gil)` and automatically get all the relevant information like include directory, transitive dependencies and any required compilation flags.

---

## Comment 1

> Username: mloskot  
> Created at: 2018-11-12 13:22:39 UTC  
> Url: https://github.com/boostorg/gil/issues/167#issuecomment-437877861  

See my answer to https://github.com/boostorg/gil/issues/168#issuecomment-437877776

---

## Comment 2

> Username: sdebionne  
> Created at: 2018-12-06 11:42:42 UTC  
> Url: https://github.com/boostorg/gil/issues/167#issuecomment-444844212  

GIL being a header only library, I guess `Boost::boost` should be enough? Or are you using Boost.GIL standalone (if that is even possible)?

---

## Comment 3

> Username: mloskot  
> Created at: 2018-12-06 12:44:18 UTC  
> Updated at: 2018-12-06 12:45:34 UTC  
> Url: https://github.com/boostorg/gil/issues/167#issuecomment-444859009  

`Boost::boost` should be enough.  
  
It is not possible to use Boost.GIL without number of Boost header-only libraries. Namely:  
  
https://github.com/boostorg/gil/blob/e3912d81c4417125ab7918cc6e8d9252d7492bd0/.travis.yml#L195-L247  
  
Efforts to cut down dependency on Boost libraries is ongoing. I'm currently eliminating Boost.MPL and Boost.TypeTraits, hoping to complete it this month. That is part of the breaking C++11 transition mentioned in https://github.com/boostorg/gil/pull/172#issuecomment-444857197

---

## Comment 4

> Username: mloskot  
> Created at: 2019-01-11 20:56:04 UTC  
> Url: https://github.com/boostorg/gil/issues/167#issuecomment-453653831  

I've labelled this as _work-in-progress_ as this idea is not dropped, but postponed until the CMake for Boost has been completed.  
Apparently, some interesting CMake-ification efforts are ongoing thanks to @Mike-Devel & @pdimov, so chances are the GIL's own CMake configuration will need to be modified anyway. Let's wait and see.

---

## Comment 5

> Username: SergMariaDB  
> Created at: 2022-12-15 07:03:16 UTC  
> Url: https://github.com/boostorg/gil/issues/167#issuecomment-1352639715  

> GIL being a header only library, I guess `Boost::boost` should be enough? Or are you using Boost.GIL standalone (if that is even possible)?  
  
For example:  
`[build] /usr/local/include/boost/gil/extension/io/jpeg/tags.hpp:26:10: fatal error: 'jerror.h' file not found`  
this issue could be resolved using the Boost::Gil target (jpeg dependency passthrough)
