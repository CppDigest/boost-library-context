# #275 - Unexpected floor division with ndarrays on python 3 [Open]

> Username: colinRawlings  
> Created at: 2019-10-23 15:45:05 UTC  
> Updated at: 2019-10-23 15:45:05 UTC  
> Url: https://github.com/boostorg/python/issues/275  

Our primarily C++ code base uses an embedded python to do computation with help from `boost::python`.  We are in the process of porting to python 3.  We have noticed that it appears that the `/` operator when acting on a pair of `boost::python::numpy::ndarray`'s yields floor division.  That is to say the results are numerically consistent with constructing the same arrays in a python interpreter and then performing `arr1 // arr2`.  We do did not see this behaviour when linking against `python27.dll`.  To make my issue concrete I prepared an example based on those provided in the `examples/` folder.  It was built on windows 10 using:  
  
- MSVC 2015  
- Python 2.7.16  
- Python 3.6.8  
- cmake 3.16  
- Ninja 1.9.0  
- Boost 1.70.0  
   
`main.cpp` was:  
```cpp  
#include <boost/python/numpy.hpp>  
#include <iostream>  
  
namespace p = boost::python;  
namespace np = boost::python::numpy;  
  
typedef float arr_dtype;  
  
int main(int argc, char **argv)  
{  
  Py_Initialize();  
  np::initialize();  
  
  arr_dtype raw_array1[] = {1.53f, 2.53f, 3.53f, 4.53f};  
  np::ndarray nd_array1 = np::from_data(raw_array1, np::dtype::get_builtin<arr_dtype>(),  
                                        p::make_tuple(4),  
                                        p::make_tuple(4),  
                                        p::object());  
  
  arr_dtype raw_array2[] = {0.1f};  
  np::ndarray nd_array2 = np::from_data(raw_array2, np::dtype::get_builtin<arr_dtype>(),  
                                        p::make_tuple(1),  
                                        p::make_tuple(1),  
                                        p::object());  
  
  std::cout << "ndarray 1 is " << p::extract<char const *>(p::str(nd_array1)) << std::endl;  
  std::cout << "ndarray 2 is " << p::extract<char const *>(p::str(nd_array2)) << std::endl;  
  
  //  
  
  p::object result_array_div = nd_array1 / nd_array2;  
  
  std::cout << "operator/ division of arrays is " << p::extract<char const *>(p::str(result_array_div)) << std::endl;  
  
  return 0;  
}  
```  
For Python 27 the output when running `main.exe` was consistent with our expectations:  
```  
ndarray 1 is [1.53 2.53 3.53 4.53]  
ndarray 2 is [0.1]  
operator/ division of arrays is [15.299999 25.3      35.3      45.300003]  
```  
  
For Python 36 the result when running `main.exe` was not consistent with our (possibly misguided) expectations:  
```  
ndarray 1 is [1.53 2.53 3.53 4.53]  
ndarray 2 is [0.1]  
operator/ division of arrays is [15. 25. 35. 45.]  
```  
Is this expected behaviour?  Is there any way to enforce non-floor division when using the `/` operator and python 3?  
  
For reference the `CMakeLists.txt` was:   
 ```  
cmake_minimum_required(VERSION 3.16)  
  
project(test_boost_numpy_division)  
  
# version selection  
  
if (BUILD_PY3)  
    set(python_package_name "Python3")  
    set(BOOST_ROOT "${BOOST_ROOT_PY3}")  
    set(py_dll_path "${Python3_ROOT_DIR}/python36.dll")  
    set(py_maj_min_version "36")  
else()  
    set(python_package_name "Python2")  
    set(BOOST_ROOT "${BOOST_ROOT_PY2}")  
    set(py_dll_path "C:/Windows/System32/python27.dll")  
    set(py_maj_min_version "27")  
endif()  
  
set(boost_python_module_name "python${py_maj_min_version}")  
set(boost_numpy_module_name "numpy${py_maj_min_version}")  
  
# python config  
  
find_package (${python_package_name} COMPONENTS Development REQUIRED)  
  
# boost config  
  
set(Boost_USE_MULTITHREADED ON)  
set(Boost_USE_STATIC_LIBS OFF)  
  
find_package(Boost REQUIRED COMPONENTS ${boost_python_module_name} ${boost_numpy_module_name} unit_test_framework)  
  
#  
  
add_executable(main)  
target_sources(main PRIVATE main.cpp)  
  
target_link_libraries(main ${python_package_name}::Python)  
target_link_libraries(main Boost::${boost_python_module_name} Boost::${boost_numpy_module_name})  
  
set_target_properties( main  
    PROPERTIES  
    ARCHIVE_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/bin"  
    RUNTIME_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/bin"  
)  
  
#   
  
file(COPY "${Boost_LIBRARY_DIRS}/" DESTINATION "${CMAKE_BINARY_DIR}/bin")  
file(COPY "${py_dll_path}" DESTINATION "${CMAKE_BINARY_DIR}/bin")  
```
