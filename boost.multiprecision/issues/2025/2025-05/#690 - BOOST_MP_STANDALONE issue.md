# #690 - BOOST_MP_STANDALONE issue [Open]

> Username: andreygorbachev  
> Created at: 2025-05-23 09:08:18 UTC  
> Updated at: 2025-06-11 08:15:26 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/690  

I am trying to use multiprecision in the standalone mode (with cmake and FetchContent), so in my CMakeLists.txt I have:  
  
#####  
include(FetchContent)  
  
set(BOOST_INCLUDE_LIBRARIES multiprecision)  
set(BOOST_MP_STANDALONE TRUE)  
  
FetchContent_Declare(  
  Boost  
  GIT_REPOSITORY https://github.com/boostorg/boost.git  
  GIT_TAG        boost-1.88.0  
)  
FetchContent_MakeAvailable(Boost)  
#####  
  
Reading your README.md I see that "Defining BOOST_MP_STANDALONE allows Boost.Multiprecision to be used with the only dependency being Boost.Config.".  
  
But the generated .sln file (I use Visual Studio) shows boost_assert, boost_container, boost_core, boost_math, boost_random, boost_system and boost_variant2 - is this expected? (feels like more than just the Boost.Config dependency, but apologies if I misunderstood Boost.Config).  
  
Thank you

---

## Comment 1

> Username: jzmaddock  
> Created at: 2025-05-23 11:21:52 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/690#issuecomment-2904111721  

Unfortunately the CMake files have no knowledge of that define and so pull in everything :(  
  
I don't use or frankly understand CMake myself, so we might need some help to make that work (@mborland ?), but yes we should do something about that otherwise those other libraries get needlessly pulled down.

---

## Comment 2

> Username: mborland  
> Created at: 2025-05-23 12:20:42 UTC  
> Updated at: 2025-05-23 12:57:02 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/690#issuecomment-2904244700  

I don't believe the `set` commands apply to `FetchContent`. Since you have the repository set to the boost superproject this is pulling all the dependencies. I think that if you do:  
  
```  
FetchContent_Declare(  
Boost_mp_standalone  
GIT_REPOSITORY https://github.com/boostorg/multiprecision.git  
GIT_TAG Boost_1_88_0  
)  
FetchContent_MakeAvailable(Boost_mp_standalone)  
```  
  
Our releases page contains a bundled copy of boost.config so that pulling the release package down will work as expected.

---

## Comment 3

> Username: andreygorbachev  
> Created at: 2025-05-23 13:10:17 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/690#issuecomment-2904372620  

Thank you - I'll try your suggestion later on today and will report back. I also have limited CMake knowledge unfortunately.  
  
set(BOOST_INCLUDE_LIBRARIES multiprecision)  
  
is something which works to select only multiprecision from all of the Boost libraries, which made me think that the set command would apply to FetchContent (but maybe that meant for find_package only).

---

## Comment 4

> Username: andreygorbachev  
> Created at: 2025-05-23 13:35:26 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/690#issuecomment-2904449877  

I can confirm that your suggestion indeed works. I do note that the generated .sln solution does not list boost_multiprecision as a project, but that is just a minor inconvenience, rather than anything else (it was not listed in my original attempt also, just dependencies were listed, so it well could be that I am still not getting everything right in my CMake setup). Thank you again.

---

## Comment 5

> Username: jzmaddock  
> Created at: 2025-05-23 15:11:35 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/690#issuecomment-2904771996  

Does it need to generate a VS project?  The library is header only and there is nothing to actually build.  Again, I'm not sure what the normal CMake behaviour would be.  
  
BTW since you're using Visual Studio, you could just download the zip, add our /include/ directory to your VS include search paths and be done.

---

## Comment 6

> Username: andreygorbachev  
> Created at: 2025-05-24 09:07:50 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/690#issuecomment-2906670963  

I had a look at what does variant2 do - it seems that a project is generated (indeed in both cases there is nothing to "build" - both are header only libraries). Could be that a .natvis file what makes a difference here (there is one in variant2, but maybe not in multiprecision?). varaint2 does not include its dependencies for CMake, so I also had to add mp11 manually and that library did not generate a project. I'll have a look at conceptual differences (if any) between CMakeLists.txt between these projects when I have a minute.  
  
Thank you for pointing out a possibility of just downloading the zip file. Still I would like to consider the CMake as much as possible (as it is widely used in the C++ community). I do use Visual Studio (also means that a .natvis file would be very useful BTW - did not know that some Boost libraries actually provide one), but I would like my work not to be tide up to this platform.

---

## Comment 7

> Username: andreygorbachev  
> Created at: 2025-06-11 08:15:26 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/690#issuecomment-2961674197  

It seems that this is the part of variant2 library, which generates the VS project :   
  
if(CMAKE_VERSION VERSION_GREATER 3.18 AND CMAKE_GENERATOR MATCHES "Visual Studio")  
  
  file(GLOB_RECURSE boost_variant2_IDEFILES CONFIGURE_DEPENDS include/*.hpp)  
  source_group(TREE ${PROJECT_SOURCE_DIR}/include FILES ${boost_variant2_IDEFILES} PREFIX "Header Files")  
  list(APPEND boost_variant2_IDEFILES extra/boost_variant2.natvis)  
  target_sources(boost_variant2 PRIVATE ${boost_variant2_IDEFILES})  
  
endif()  
  
which is missing from this library. Would you please consider adding something like that to your project?  
  
IMHO it would be even better to explicitly list the headers in add_library, so something like:   
  
add_library(boost_multiprecision INTERFACE  
  <header1>.h  
  <header2>.h  
)  
  
but maybe this is outside the style of boost libraries.  
  
Yes, these libraries are header only, but their headers do "belong" to a library, so (at least to me) telling cmake what headers are in the library is the right thing to do.  
  
*.natvis file would also be great for your project, is it something you could also consider (as several other boost libraries provide it)? Should I create a separate issue for it?
