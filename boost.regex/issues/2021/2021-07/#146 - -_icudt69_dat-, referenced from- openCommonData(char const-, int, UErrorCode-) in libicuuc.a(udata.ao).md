# #146 - "_icudt69_dat", referenced from:       openCommonData(char const*, int, UErrorCode*) in libicuuc.a(udata.ao) [Closed]

> Username: Alababdiy  
> Created at: 2021-07-30 17:55:49 UTC  
> Updated at: 2022-12-29 18:37:17 UTC  
> Closed at: 2021-07-30 19:06:24 UTC  
> Url: https://github.com/boostorg/regex/issues/146  

Error:-  
Undefined symbols for architecture x86_64:  
  "_icudt69_dat", referenced from:  
      openCommonData(char const*, int, UErrorCode*) in libicuuc.a(udata.ao)  
ld: symbol(s) not found for architecture x86_64  
  
  
my code :-   
  
```  
#include <boost/regex/icu.hpp>  
#include <boost/regex.hpp>  
....  
.  
.  
.  
{  
auto expr = boost::make_u32regex("[\\x{064B}-\\x{065B}]");  
auto text =boost::u32regex_replace(text,expr,"") ;  
}  
```  
  
CMakeLists.txt :--  
```  
cmake_minimum_required(VERSION 3.5)  
project(webhook.ratina CXX)  
  
include(CheckIncludeFileCXX)  
  
check_include_file_cxx(any HAS_ANY)  
check_include_file_cxx(string_view HAS_STRING_VIEW)  
check_include_file_cxx(coroutine HAS_COROUTINE)  
if (HAS_ANY AND HAS_STRING_VIEW AND HAS_COROUTINE)  
    set(CMAKE_CXX_STANDARD 20)  
elseif (HAS_ANY AND HAS_STRING_VIEW)  
    set(CMAKE_CXX_STANDARD 17)  
else ()  
    set(CMAKE_CXX_STANDARD 14)  
endif ()  
  
set(CMAKE_CXX_STANDARD_REQUIRED ON)  
set(CMAKE_CXX_EXTENSIONS OFF)  
  
add_executable(${PROJECT_NAME} main.cc functions.h)  
  
# ##############################################################################  
# If you include the drogon source code locally in your project, use this method  
# to add drogon   
# add_subdirectory(drogon)   
# target_link_libraries(${PROJECT_NAME} PRIVATE drogon)  
#  
# and comment out the following lines  
find_package(Drogon CONFIG REQUIRED)  
  
target_link_libraries(${PROJECT_NAME} PRIVATE Drogon::Drogon)  
  
# ##############################################################################  
  
if (CMAKE_CXX_STANDARD LESS 17)  
    # With C++14, use boost to support any and string_view  
    message(STATUS "use c++14")  
    find_package(Boost 1.61.0 REQUIRED)  
    target_include_directories(${PROJECT_NAME} PRIVATE ${Boost_INCLUDE_DIRS})  
elseif (CMAKE_CXX_STANDARD LESS 20)  
    message(STATUS "use c++17")  
else ()  
    message(STATUS "use c++20")  
endif ()  
  
aux_source_directory(controllers CTL_SRC)  
aux_source_directory(filters FILTER_SRC)  
aux_source_directory(plugins PLUGIN_SRC)  
aux_source_directory(models MODEL_SRC)  
  
  
  
  
drogon_create_views(${PROJECT_NAME} ${CMAKE_CURRENT_SOURCE_DIR}/views  
                    ${CMAKE_CURRENT_BINARY_DIR})  
# use the following line to create views with namespaces.  
# drogon_create_views(${PROJECT_NAME} ${CMAKE_CURRENT_SOURCE_DIR}/views  
#                     ${CMAKE_CURRENT_BINARY_DIR} TRUE)  
  
target_include_directories(${PROJECT_NAME}  
                           PRIVATE ${CMAKE_CURRENT_SOURCE_DIR}  
                                   ${CMAKE_CURRENT_SOURCE_DIR}/models)  
target_sources(${PROJECT_NAME}  
               PRIVATE  
               ${SRC_DIR}  
               ${CTL_SRC}  
               ${FILTER_SRC}  
               ${PLUGIN_SRC}  
               ${MODEL_SRC})  
  
find_package(cpr CONFIG REQUIRED)  
find_package(fmt CONFIG REQUIRED)  
find_package(nlohmann_json CONFIG REQUIRED)  
find_package(unofficial-tinyexpr CONFIG REQUIRED)  
  
find_package(Boost 1.76.0 CONFIG REQUIRED)  
target_link_libraries(${PROJECT_NAME} ${Boost_LIBRARIES})  
find_package(ICU 61.0 COMPONENTS uc i18n REQUIRED)  
target_link_libraries(${PROJECT_NAME} PRIVATE cpr nlohmann_json fmt::fmt ICU::uc ICU::i18n unofficial::tinyexpr::tinyexpr ${ZLIB_LIBRARY})  
  
  
  
# ##############################################################################  
# uncomment the following line for dynamically loading views   
# set_property(TARGET ${PROJECT_NAME} PROPERTY ENABLE_EXPORTS ON)  
  
```

---

## Comment 1

> Username: jzmaddock  
> Created at: 2021-07-30 19:06:24 UTC  
> Url: https://github.com/boostorg/regex/issues/146#issuecomment-890095989  

Since the undefined symbol is coming from *within* ICU you need to address the issue there.  Interestingly, Google shows zero hits for that symbol which isn't encouraging :(

---

## Comment 2

> Username: random-yang  
> Created at: 2022-12-29 18:37:17 UTC  
> Url: https://github.com/boostorg/regex/issues/146#issuecomment-1367513951  

Add `icudata` to the link target. This works for me.
