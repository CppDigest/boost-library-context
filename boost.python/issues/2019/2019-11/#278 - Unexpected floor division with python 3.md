# #278 - Unexpected floor division with python 3 [Closed]

> Username: colinRawlings  
> Created at: 2019-11-15 13:11:50 UTC  
> Updated at: 2022-12-07 15:06:45 UTC  
> Closed at: 2022-12-07 15:06:45 UTC  
> Url: https://github.com/boostorg/python/issues/278  

Our primarily C++ code base uses an embedded python to do computation with help from boost python.  We are in the process of porting to python 3.  We have noticed that it appears that the `/` operator when acting on a pair of `boost::python::object`'s yields floor division.  That is to say the results are numerically consistent with constructing the same variables in a python interpreter and then performing `variable_1 // variable_2`.  We do did not see this behaviour when linking against `python27.dll`.  To make my issue concrete I prepared a small example.  It was built on windows 10 using:  
  
- MSVC 2015  
- Python 2.7.16  
- Python 3.6.8  
- cmake 3.15.5  
- Ninja 1.9.0  
- Boost 1.70.0  
   
`main.cpp` was:  
```cpp  
#include <iostream>  
#include <boost/python.hpp>  
  
namespace p = boost::python;  
  
int main(int argc, char **argv)  
{  
  Py_Initialize();  
  
  p::object v1{4.5};  
  p::object v2{2.5};  
  
  p::object result = v1 / v2;  
  std::cout << "version: " << PY_VERSION << ", " << p::extract<char const *>(p::str(v1)) << " / " << p::extract<char const *>(p::str(v2)) << " = ";  
  std::cout << p::extract<char const *>(p::str(result)) << std::endl;  
  
  return 0;   
}  
```  
For Python 27 the output when building and running `main.exe` was consistent with our expectations:  
```  
version: 2.7.16, 4.5 / 2.5 = 1.8  
```  
  
For Python 36 the result when building and running `main.exe` was not consistent with our (possibly misguided) expectations:  
```  
version: 3.6.8, 4.5 / 2.5 = 1.0  
```  
Is this expected behaviour?  Is there any way to enforce non-floor division when using the `/` operator and python 3?  We see the [same behaviour](https://github.com/boostorg/python/issues/275) when working with `ndarray`'s.  
  
For reference the `CMakeLists.txt` was:   
 ```  
cmake_minimum_required(VERSION 3.12)  
  
project(test_boost_division)  
  
# version selection  
  
IF (NOT DEFINED THIRD_PARTY_LIB_DIR )   
    message(STATUS "THIRD_PARTY_LIB_DIR must be defined: ${THIRD_PARTY_LIB_DIR}")  
ENDIF()  
  
IF(NOT EXISTS "${THIRD_PARTY_LIB_DIR}")  
    message(STATUS "THIRD_PARTY_LIB_DIR does not exist: ${THIRD_PARTY_LIB_DIR}")  
ENDIF()  
  
set(python_package_name "Python${PY_MAJOR_VERSION}")  
set(BOOST_ROOT "${THIRD_PARTY_LIB_DIR}/boost/boost_1_70_0_py${PY_MAJOR_VERSION}")  
  
set(boost_python_module_name "python${PY_MAJOR_VERSION}${PY_MINOR_VERSION}")  
  
# python config  
  
find_package (${python_package_name} COMPONENTS Development REQUIRED)  
  
if (PY_MAJOR_VERSION STREQUAL "3")  
    set(py_dll_path "${Python3_INCLUDE_DIRS}/../python36.dll")  
else()  
    set(py_dll_path "C:/Windows/System32/python27.dll")  
endif()     
  
# boost config  
  
IF(NOT EXISTS "${BOOST_ROOT}")  
    message(STATUS "BOOST_ROOT does not exist: ${BOOST_ROOT}")  
ENDIF()  
  
set(Boost_USE_MULTITHREADED ON)  
set(Boost_USE_STATIC_LIBS OFF)  
  
find_package(Boost REQUIRED COMPONENTS ${boost_python_module_name} unit_test_framework)  
  
#  
  
add_executable(main)  
target_sources(main PRIVATE main.cpp)  
  
target_link_libraries(main ${python_package_name}::Python)  
target_link_libraries(main Boost::${boost_python_module_name})  
  
set_target_properties( main  
    PROPERTIES  
    ARCHIVE_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/bin"  
    RUNTIME_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/bin"  
)  
  
#   
  
file(COPY "${Boost_LIBRARY_DIRS}/" DESTINATION "${CMAKE_BINARY_DIR}/bin")  
file(COPY "${py_dll_path}" DESTINATION "${CMAKE_BINARY_DIR}/bin")  
```

---

## Comment 1

> Username: michaeljefferies  
> Created at: 2022-11-15 18:48:08 UTC  
> Url: https://github.com/boostorg/python/issues/278#issuecomment-1315726138  

I looked into this and the reason for this behavior is by design.  See the comment here: https://github.com/boostorg/python/blob/f5d14ef15e98838b3cf18692d46c481287ed50d0/src/object_operators.cpp#L42  
  
// We choose FloorDivide instead of TrueDivide to keep the semantic  
// conform with C/C++'s '/' operator  
  
In python 3, PyNumber_Divide no longer exists - there is PyNumber_FloorDivide (which is python 3's // operator) and PyNumber_TrueDivide (which is python 3's / operator).  So they have opted to make things behave like the C/C++ / operator for integers by wrapping floor divide.  If they had chosen PyNumber_TrueDivide, then dividing two integer python objects would have resulted in a float python object.

---

## Comment 2

> Username: colinRawlings  
> Created at: 2022-12-07 15:06:45 UTC  
> Url: https://github.com/boostorg/python/issues/278#issuecomment-1341102209  

Thanks for taking a look and providing the rational.
