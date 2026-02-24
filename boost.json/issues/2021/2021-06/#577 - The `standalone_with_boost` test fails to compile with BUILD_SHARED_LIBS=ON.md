# #577 - The `standalone_with_boost` test fails to compile with BUILD_SHARED_LIBS=ON [Closed]

> Username: pdimov  
> Created at: 2021-06-05 00:55:55 UTC  
> Updated at: 2021-06-28 07:14:05 UTC  
> Closed at: 2021-06-28 07:14:05 UTC  
> Url: https://github.com/boostorg/json/issues/577  

Building the CMake tests (from the Boost superproject) with `BUILD_SHARED_LIBS=ON` fails with  
```  
[2/4] Building CXX object libs\json\test\CMakeFiles\boost_json-tests.dir\standalone_with_boost.cpp.obj  
FAILED: libs/json/test/CMakeFiles/boost_json-tests.dir/standalone_with_boost.cpp.obj  
C:\PROGRA~2\MIB055~1\2019\COMMUN~1\VC\Tools\MSVC\1429~1.300\bin\Hostx64\x64\cl.exe  /nologo /TP -DBOOST_CONTAINER_DYN_LINK -DBOOST_CONTAINER_NO_LIB -DBOOST_JSON_DYN_LINK=1 -DBOOST_JSON_NO_LIB=1 -I..\libs\json\test\. -I..\libs\json\include -I..\libs\align\include -I..\libs\assert\include -I..\libs\config\include -I..\libs\core\include -I..\libs\static_assert\include -I..\libs\container\include -I..\libs\intrusive\include -I..\libs\container_hash\include -I..\libs\detail\include -I..\libs\preprocessor\include -I..\libs\type_traits\include -I..\libs\integer\include -I..\libs\throw_exception\include -I..\libs\move\include -I..\libs\winapi\include -I..\libs\predef\include -I..\libs\exception\include -I..\libs\smart_ptr\include -I..\libs\tuple\include -I..\libs\mp11\include -I..\libs\system\include -I..\libs\utility\include -I..\libs\io\include /DWIN32 /D_WINDOWS /GR /EHsc /Zi /Ob0 /Od /RTC1 -MDd /showIncludes /Folibs\json\test\CMakeFiles\boost_json-tests.dir\standalone_with_boost.cpp.obj /Fdlibs\json\test\CMakeFiles\boost_json-tests.dir\ /FS -c ..\libs\json\test\standalone_with_boost.cpp  
C:\boost-git\develop\libs\json\include\boost/json/impl/array.ipp(30): warning C4273: 'empty_': inconsistent dll linkage  
C:\boost-git\develop\libs\json\include\boost/json/array.hpp(94): note: see previous definition of 'private: static boost::json::array::table boost::json::array::empty_'  
C:\boost-git\develop\libs\json\include\boost/json/impl/array.ipp(30): error C2491: 'boost::json::array::empty_': definition of dllimport static data member not allowed  
C:\boost-git\develop\libs\json\include\boost/json/impl/array.ipp(39): warning C4273: 'boost::json::array::table::allocate': inconsistent dll linkage  
C:\boost-git\develop\libs\json\include\boost/json/impl/array.hpp(41): note: see previous definition of 'allocate'  
```  
followed by many more "inconsistent dll linkage" warnings.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2021-06-08 19:54:00 UTC  
> Url: https://github.com/boostorg/json/issues/577#issuecomment-857068831  

Also  
```  
boost_json-vc142-mt-gd-x64-1_77.lib(boost_json-vc142-mt-gd-x64-1_77.dll) : error LNK2005: "public: __cdecl boost::json:  
:detail::stack::~stack(void)" (??1stack@detail@json@boost@@QEAA@XZ) already defined in standalone_with_boost.obj [C:\bo  
ost-git\master\__build\libs\json\test\boost_json-tests.vcxproj]  
```
