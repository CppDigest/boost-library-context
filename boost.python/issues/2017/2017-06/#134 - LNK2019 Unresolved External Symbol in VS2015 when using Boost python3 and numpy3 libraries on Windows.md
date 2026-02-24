# #134 - LNK2019 Unresolved External Symbol in VS2015 when using Boost python3 and numpy3 libraries on Windows [Closed]

> Username: oysteinskotheim  
> Created at: 2017-06-09 10:54:43 UTC  
> Updated at: 2017-08-06 21:14:13 UTC  
> Closed at: 2017-08-06 21:14:13 UTC  
> Url: https://github.com/boostorg/python/issues/134  

I get the following linking error when trying to ust boost::python::numpy on Windows:  
  
    test_boost_python.obj : error LNK2019: unresolved external symbol "class boost::python::numpy::dtype __cdecl boost::python::numpy::detail::get_float_dtype<32>(void)" (??$get_float_dtype@$0CA@@detail@numpy@python@boost@@YA?AVdtype@123@XZ) referenced in function "public: static class boost::python::numpy::dtype __cdecl boost::python::numpy::detail::builtin_dtype<float,0>::get(void)" (?get@?$builtin_dtype@M$0A@@detail@numpy@python@boost@@SA?AVdtype@345@XZ)  
  
I have downloaded and built Boost 1.64 on Windows by using the following command:  
  
    b2 --build-type=complete address-model=64 toolset=msvc stage  
  
I added a user-config.jam file in my home directory to tell Boost where to find Python 3:  
  
    using python : 3.6 : c:\\anaconda3\\python ;  
  
I am then trying to compile a small test project using boost::python and boost::numpy:  
  
    #include <boost/python.hpp>  
    #include <boost/python/numpy.hpp>  
  
    namespace bp = boost::python;  
    namespace np = boost::python::numpy;  
  
    np::ndarray test_make_zeros(int rows, int cols)  
    {  
        return np::zeros(bp::make_tuple(rows, cols), np::dtype::get_builtin<float>());    
    }  
  
    BOOST_PYTHON_MODULE(test_boost_numpy)  
    {  
        np::initialize();  
          
        bp::def("test_make_zeros", test_make_zeros);  
    }  
  
I am using the following CMakeLists.txt file:  
  
    cmake_minimum_required(VERSION 3.8)  
    project(test_boost_python)  
  
    set(BOOST_ROOT "C:/Boost-1.64")  
    SET(Boost_ADDITIONAL_VERSIONS 1.64)  
  
    find_package(PythonLibs 3.6 REQUIRED)  
    include_directories(${PYTHON_INCLUDE_DIRS})  
  
    find_package(Boost REQUIRED COMPONENTS python3 numpy3)  
    include_directories(${Boost_INCLUDE_DIRS})  
    link_directories(${Boost_LIBRARY_DIRS})  
  
    add_library(test_boost_python SHARED test_boost_python.cpp)  
    set_target_properties(test_boost_python PROPERTIES PREFIX "" SUFFIX ".pyd")  
    set_target_properties(test_boost_python PROPERTIES DEFINE_SYMBOL "BOOST_ALL_NO_LIB")  
    target_link_libraries(test_boost_python ${PYTHON_LIBRARIES} ${Boost_LIBRARIES})  
  
My boost::python library is given the name boost_python3-vc140-mt-1_64.lib and boost::numpy ends up as boost_numpy3-vc140-mt-1_64.lib when linking against Python 3.6.  
  
I had to turn on BOOST_ALL_NO_LIB. If not, the compiler looks for boost_python-vc140-mt-1_64.lib boost_numpy-vc140-mt-1_64.lib (which is under the wrong name, with a missing number 3 after the library names; perhaps a separate bug)

---

## Comment 1

> Username: oysteinskotheim  
> Created at: 2017-06-10 11:22:49 UTC  
> Url: https://github.com/boostorg/python/issues/134#issuecomment-307559248  

This seems to be related to #125 and commit [3844c4](https://github.com/boostorg/python/commit/3844c4fc5fa44dcc2b74deb3b2cb1935175ef407) Problem disappeared using latest develop branch from github. It does not appear to have been merged into master yet.

---

## Comment 2

> Username: stefanseefeld  
> Created at: 2017-06-27 21:36:27 UTC  
> Url: https://github.com/boostorg/python/issues/134#issuecomment-311493364  

I just merged `develop` to `master`. Please try again and let me know whether there is still an issue. Thanks !

---

## Comment 3

> Username: oysteinskotheim  
> Created at: 2017-08-06 21:11:11 UTC  
> Updated at: 2017-08-06 21:12:01 UTC  
> Url: https://github.com/boostorg/python/issues/134#issuecomment-320532976  

I have tested with Boost 1.65 beta 1 now and I can confirm that the linker errors have gone away.  
  
I get several warnings from CMake now, but it still works. I also had to set the BOOST_INCLUDE_DIR manually (it was not enough to set BOOST_ROOT and Boost_ADDITIONAL_VERSIONS). I am not sure where to direct questions regarding Boost and CMake?  
  
```  
CMake Warning at C:/Program Files/CMake/share/cmake-3.9/Modules/FindBoost.cmake:769 (message):  
  Imported targets not available for Boost version 106500  
Call Stack (most recent call first):  
  C:/Program Files/CMake/share/cmake-3.9/Modules/FindBoost.cmake:873 (_Boost_COMPONENT_DEPENDENCIES)  
  C:/Program Files/CMake/share/cmake-3.9/Modules/FindBoost.cmake:1501 (_Boost_MISSING_DEPENDENCIES)  
  CMakeLists.txt:10 (find_package)  
  
CMake Warning at C:/Program Files/CMake/share/cmake-3.9/Modules/FindBoost.cmake:769 (message):  
  Imported targets not available for Boost version 106500  
Call Stack (most recent call first):  
  C:/Program Files/CMake/share/cmake-3.9/Modules/FindBoost.cmake:873 (_Boost_COMPONENT_DEPENDENCIES)  
  C:/Program Files/CMake/share/cmake-3.9/Modules/FindBoost.cmake:1501 (_Boost_MISSING_DEPENDENCIES)  
  CMakeLists.txt:10 (find_package)  
  
CMake Warning at C:/Program Files/CMake/share/cmake-3.9/Modules/FindBoost.cmake:1564 (message):  
  No header defined for python3; skipping header check  
Call Stack (most recent call first):  
  CMakeLists.txt:10 (find_package)  
  
CMake Warning at C:/Program Files/CMake/share/cmake-3.9/Modules/FindBoost.cmake:1564 (message):  
  No header defined for numpy3; skipping header check  
Call Stack (most recent call first):  
  CMakeLists.txt:10 (find_package)  
  
Boost version: 1.65.0  
Found the following Boost libraries:  
  python3  
  numpy3  
Configuring done  
Generating done  
```

---

## Comment 4

> Username: stefanseefeld  
> Created at: 2017-08-06 21:14:13 UTC  
> Url: https://github.com/boostorg/python/issues/134#issuecomment-320533126  

Great, thanks !   
I don't know about CMake (in particular as the Boost.Python repo itself doesn't contain any CMake logic), sorry. Please try on the Boost ML, in particular as the CMake transition is still a hot topic there.
