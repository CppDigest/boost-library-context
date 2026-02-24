# #177 - Zlib and Bz2 link targets hard coded [Open]

> Username: WolfJMZ  
> Created at: 2024-09-10 18:23:53 UTC  
> Updated at: 2025-05-06 20:16:20 UTC  
> Url: https://github.com/boostorg/iostreams/issues/177  

When using static compiled libs for zlib and bz2 - I can specify the target name when calling b2 as follows:  
  
`-s ZLIB_BINARY=zlibstatic -s BZIP2_BINARY=bz2_static`  
  
However, these target names never make it into the resulting generated cmake file:  
This was generated in libboost_iostreams-variant-vc143-mt-gd-x64-1_86-static.cmake:  
```  
if(CMAKE_CONFIGURATION_TYPES)  
  set_property(TARGET Boost::iostreams APPEND PROPERTY INTERFACE_LINK_LIBRARIES  
    "$<$<CONFIG:debug>:bz2;z>")  
else()  
  set_property(TARGET Boost::iostreams APPEND PROPERTY INTERFACE_LINK_LIBRARIES  
    bz2 z)  
endif()  
```  
  
I see there is a merged PR that addesses this: https://github.com/boostorg/iostreams/pull/176  
  
However this is really a bug since the name being provided to b2 isn't making it down to the generated cmake configuration files.

---

## Comment 1

> Username: addy90  
> Created at: 2025-05-06 18:36:20 UTC  
> Url: https://github.com/boostorg/iostreams/issues/177#issuecomment-2855554827  

I am running into the same issue, unfortunately. Using custom compiled static zlib and bzip2 the compilation works, but the linking wants to link with "-lbz2", which does not exist on the system in that way, so linking fails.

---

## Comment 2

> Username: WolfJMZ  
> Created at: 2025-05-06 19:21:39 UTC  
> Url: https://github.com/boostorg/iostreams/issues/177#issuecomment-2855664542  

In boost 1.88 (and older versions back to 1.85) - I just end up going into the output build folder and modifying the cmake file to remove that linker command.  
  
Here's the last few lines I removed;  
  
  
```  
if(CMAKE_CONFIGURATION_TYPES)  
  set_property(TARGET Boost::iostreams APPEND PROPERTY INTERFACE_LINK_LIBRARIES  
    "$<$<CONFIG:release>:bz2;z>")  
else()  
  set_property(TARGET Boost::iostreams APPEND PROPERTY INTERFACE_LINK_LIBRARIES  
    bz2 z)  
endif()  
  
```  
  
The file is <path to binary output folder>\lib\cmake\boost_iostreams-1.88.0\libboost_iostreams-variant-vc143-mt-x64-1_88-static.cmake  
  
If I remove the lines above and just let my cmake  linker handle bz2 and zlib, it still compiles fine and works fine. It's just the cmake detection in boost iostreams of static bz2 and static zlib that doesn't work.

---

## Comment 3

> Username: addy90  
> Created at: 2025-05-06 19:44:26 UTC  
> Updated at: 2025-05-06 19:45:27 UTC  
> Url: https://github.com/boostorg/iostreams/issues/177#issuecomment-2855755485  

> In boost 1.88 (and older versions back to 1.85) - I just end up going into the output build folder and modifying the cmake file to remove that linker command.  
  
Thanks for the info, I thought about that, too, but I would have to automate that for GCC, Clang, and MSVC on Linux and Windows for my project, so I am a bit hesitant for such a "hack", although it would work for sure.  
  
But I had another idea, in my CMake project, I now do the following workaround:  
```CMake  
find_package(Boost REQUIRED COMPONENTS iostreams)  
get_target_property(BOOST_IOSTREAM_LIBS Boost::iostreams INTERFACE_LINK_LIBRARIES)  
list(REMOVE_ITEM BOOST_IOSTREAM_LIBS "bz2" "z")  
set_property(TARGET Boost::iostreams PROPERTY INTERFACE_LINK_LIBRARIES "${BOOST_IOSTREAM_LIBS}")  
```  
  
So I "simply" remove the link properties later. This has to occur after the final `find_package` call or it's overriden again by the original setting. But that also works.

---

## Comment 4

> Username: WolfJMZ  
> Created at: 2025-05-06 20:16:19 UTC  
> Url: https://github.com/boostorg/iostreams/issues/177#issuecomment-2855853127  

That's a great fix! I might end up using that myself!
