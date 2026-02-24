# #64 - Exported CMake config should use thirdparty lib targets and preferably find them on its own [Open]

> Username: jpfeuffer  
> Created at: 2023-05-23 17:19:38 UTC  
> Updated at: 2023-05-23 18:27:14 UTC  
> Url: https://github.com/boostorg/boost_install/issues/64  

```  
if(CMAKE_CONFIGURATION_TYPES)  
  set_property(TARGET Boost::iostreams APPEND PROPERTY INTERFACE_LINK_LIBRARIES  
    "$<$<CONFIG:release>:bz2;lzma;z;zstd>")  
else()  
  set_property(TARGET Boost::iostreams APPEND PROPERTY INTERFACE_LINK_LIBRARIES  
    bz2 lzma z zstd)  
endif()  
```  
  
This is not going to work if I just link to Boost::iostreams. How should a consumer know that those libraries are needed?  
If I am not mistaken, In modern CMake you would call `find_dependencies(ZSTD::ZSTD $Version)` and then link to the imported target in your exported CMake file.  
  
This gets especially tricky with boost::regex and ICU, where I don't even know which version I should find in my own CMake script that is "finding" boost.

---

## Comment 1

> Username: jpfeuffer  
> Created at: 2023-05-23 17:41:49 UTC  
> Url: https://github.com/boostorg/boost_install/issues/64#issuecomment-1559882159  

Using boost 1.81 from brew.
