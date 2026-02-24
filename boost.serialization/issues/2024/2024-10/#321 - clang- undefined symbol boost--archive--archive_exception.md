# #321 - clang: undefined symbol boost::archive::archive_exception [Open]

> Username: zoharl3  
> Created at: 2024-10-10 18:42:42 UTC  
> Updated at: 2024-10-10 20:08:19 UTC  
> Url: https://github.com/boostorg/serialization/issues/321  

https://stackoverflow.com/questions/79073953/clang-undefined-symbol-boostarchivearchive-exception

---

## Comment 1

> Username: sehe  
> Created at: 2024-10-10 19:23:55 UTC  
> Url: https://github.com/boostorg/serialization/issues/321#issuecomment-2405873542  

Please include the question. Links are not good descriptions.

---

## Comment 2

> Username: zoharl3  
> Created at: 2024-10-10 20:08:18 UTC  
> Url: https://github.com/boostorg/serialization/issues/321#issuecomment-2405950837  

I'm building the demo from the tutorial  
  
https://www.boost.org/doc/libs/1_85_0/libs/serialization/doc/tutorial.html  
  
I created the project using cmake for VS22 with clang-cl.exe, and the cmakefile.txt has  
  
```  
find_package( Boost REQUIRED COMPONENTS serialization )  
if(Boost_FOUND)  
    message( "Boost found: ${Boost_INCLUDE_DIRS}" )  
    target_include_directories( ${PROJECT_NAME} PRIVATE ${Boost_INCLUDE_DIRS} )  
    target_link_libraries(${PROJECT_NAME} ${Boost_LIBRARIES})  
endif()  
```  
  
and I verified that the project is linked against `..\vcpkg_installed\x64-windows\debug\lib\boost_serialization-vc143-mt-gd-x64-1_85.lib`.  
  
I get two linker errors:  
  
```  
lld-link : error : undefined symbol: __declspec(dllimport) public: void __cdecl boost::archive::archive_exception::`vbase dtor'(void)  
lld-link : error : undefined symbol: public: void __cdecl boost::archive::archive_exception::`vbase dtor'(void)  
```  
  
When building with cl.exe, there are no errors.
