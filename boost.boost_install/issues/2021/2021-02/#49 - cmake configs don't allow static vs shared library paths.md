# #49 - cmake configs don't allow static vs shared library paths [Open]

> Username: kallisti5  
> Created at: 2021-02-03 21:53:06 UTC  
> Updated at: 2021-02-03 21:56:39 UTC  
> Url: https://github.com/boostorg/boost_install/issues/49  

The cmake configs don't allow static vs shared library paths...  
  
For example, from tools/boost_install/boost-install.jam  
  
```  
/boot/system/lib/cmake/boost_wave-1.70.0/boost_wave-config.cmake:get_filename_component(_BOOST_LIBDIR "${_BOOST_CMAKEDIR}/../" ABSOLUTE)  
```  
  
That sets _BOOST_LIBDIR to the cmake dir ../ (aka /usr/lib/cmake/../ for libraries)  
  
Then all libraries are put into it...  
```  
/boot/system/lib/cmake/boost_unit_test_framework-1.70.0/libboost_unit_test_framework-variant-static.cmake:  IMPORTED_LOCATION_RELEASE "${_BOOST_LIBDIR}/libboost_unit_test_framework.a"  
/boot/system/lib/cmake/boost_unit_test_framework-1.70.0/libboost_unit_test_framework-variant-shared.cmake:  IMPORTED_LOCATION_RELEASE "${_BOOST_LIBDIR}/libboost_unit_test_framework.so.1.70.0"  
```  
  
This is not the case on all operating systems. It's a common design to have the static libraries in a different prefix path.  
Example:  
  * /boot/system/lib/libboost_unit_test_framework.so  
  * /boot/system/develop/lib/libboost_unit_test_framework.a  
  
It looks like a _BOOST_STATIC_LIBDIR or something is needed?
