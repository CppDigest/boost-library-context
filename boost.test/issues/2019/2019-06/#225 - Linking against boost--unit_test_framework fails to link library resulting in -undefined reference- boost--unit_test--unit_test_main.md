# #225 - Linking against boost::unit_test_framework fails to link library resulting in "undefined reference: boost::unit_test::unit_test_main" [Closed]

> Username: Flamefire  
> Created at: 2019-06-03 09:09:06 UTC  
> Updated at: 2019-06-03 11:11:04 UTC  
> Closed at: 2019-06-03 11:11:04 UTC  
> Url: https://github.com/boostorg/test/issues/225  

I build boost 1.70 with the default `./bootstrap && sudo ./b2 install`, then tried to link a CMake project against this via:  
  
```  
find_package(Boost 1.64 REQUIRED COMPONENTS unit_test_framework)  
target_link_libraries(myProject PRIVATE Boost::unit_test_framework)  
```  
  
When trying to build this project in debug mode the library (either static or dynamic) is not linked. I tracked this down to the generated CMake files which contain:  
  
```  
set_target_properties(Boost::unit_test_framework PROPERTIES  
  IMPORTED_LINK_INTERFACE_LANGUAGES_RELEASE CXX  
  IMPORTED_LOCATION_RELEASE "${_BOOST_LIBDIR}/libboost_unit_test_framework.so.1.70.0"  
  )  
```  
  
So only the `IMPORTED_LOCATION_RELEASE` is set. Any other location (`DEBUG; RELWITHDEBINFO`, ...) is missing.  
  
This is a show-stopper as all our tests are compiled in Debug mode, especially for coverage analysis.

---

## Comment 1

> Username: Flamefire  
> Created at: 2019-06-03 11:11:04 UTC  
> Url: https://github.com/boostorg/test/issues/225#issuecomment-498214407  

Just found the issue to be a stray `BOOST_TEST_DYN_LINK` which is defined in our CMake files depending on (among others) `Boost_UNIT_TEST_FRAMEWORK_LIBRARY` which no longer exists (only provided in the default CMake find module for boost)  
  
So my interpretation above was wrong and the CMake modules work.  
  
However they are very noisy:  
```  
Found Boost 1.70.0 at /opt/boost_1_70_0/lib/cmake/Boost-1.70.0  
  Requested configuration: QUIET REQUIRED COMPONENTS system;filesystem;iostreams  
Found boost_system 1.70.0 at /opt/boost_1_70_0/lib/cmake/boost_system-1.70.0  
  libboost_system.a  
Adding boost_system dependencies: headers  
Found boost_filesystem 1.70.0 at /opt/boost_1_70_0/lib/cmake/boost_filesystem-1.70.0  
  libboost_filesystem.a  
Adding boost_filesystem dependencies: headers  
Found boost_iostreams 1.70.0 at /opt/boost_1_70_0/lib/cmake/boost_iostreams-1.70.0  
  libboost_iostreams.a  
Adding boost_iostreams dependencies: headers  
Boost 1.64.0 found.  
Found Boost components:  
   system;filesystem;iostreams  
```  
Note especially the (wrong) output of `Boost 1.64.0 found.` which is the searched for version (minimum) not the found version -.-
