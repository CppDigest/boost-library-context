# #708 - Update boost math [Closed]

> Username: pascalpfeil  
> Created at: 2021-10-18 19:44:50 UTC  
> Updated at: 2021-11-03 15:21:51 UTC  
> Closed at: 2021-11-03 15:21:51 UTC  
> Url: https://github.com/boostorg/math/issues/708  

Could you update boost math please?  
  
When using the latest boost release with cmake `ExternalProject_Add`, https://github.com/boostorg/math/issues/675 makes my project not compile.

---

## Comment 1

> Username: pascalpfeil  
> Created at: 2021-10-18 23:51:12 UTC  
> Url: https://github.com/boostorg/math/issues/708#issuecomment-946253706  

As a workaround I am currently using  
``` cmake  
ExternalProject_Add(  
        boost_src  
        PREFIX "vendor/boost"  
        GIT_REPOSITORY "https://github.com/boostorg/boost.git"  
        GIT_TAG boost-1.77.0  
        TIMEOUT 10  
        CMAKE_ARGS  
        -DCMAKE_INSTALL_PREFIX=${CMAKE_BINARY_DIR}/vendor/boost  
        -DCMAKE_C_COMPILER=${CMAKE_C_COMPILER}  
        -DCMAKE_CXX_COMPILER=${CMAKE_CXX_COMPILER}  
        -DCMAKE_CXX_FLAGS=${CMAKE_CXX_FLAGS}  
        -DCMAKE_BUILD_TYPE:STRING=${CMAKE_BUILD_TYPE}  
        # Fix for broken boost math in 1.77.0  
        UPDATE_COMMAND cd <DOWNLOAD_DIR>/boost_src/libs/math && git checkout v1.77-standalone   
        BUILD_BYPRODUCTS <INSTALL_DIR>/lib/libboost.a  
)  
```

---

## Comment 2

> Username: jzmaddock  
> Created at: 2021-10-19 07:59:57 UTC  
> Url: https://github.com/boostorg/math/issues/708#issuecomment-946459872  

@mborland where are we with this?

---

## Comment 3

> Username: mborland  
> Created at: 2021-10-19 09:28:58 UTC  
> Url: https://github.com/boostorg/math/issues/708#issuecomment-946531090  

I'll have to investigate because #675 was fixed in his case by #676. In the broader case I definitely need to spend some time in developing a CI solution for standalone. I'm nearly with @NAThompson ask for multiprecision standalone which would need a similar CI system.

---

## Comment 4

> Username: pascalpfeil  
> Created at: 2021-10-19 16:01:05 UTC  
> Url: https://github.com/boostorg/math/issues/708#issuecomment-946869954  

Just for anyone having similar problems,  
``` cmake  
include(FetchContent)  
set(FETCHCONTENT_QUIET OFF)  
set(FETCHCONTENT_UPDATES_DISCONNECTED ON)  
set(BUILD_SHARED_LIBS OFF)  
set(CMAKE_POSITION_INDEPENDENT_CODE ON)  
set(BUILD_TESTING OFF)  
find_package(Git REQUIRED)  
  
message("Fetching boost")  
list(APPEND CMAKE_MESSAGE_INDENT "  ")  
FetchContent_Declare(  
        boost  
        GIT_REPOSITORY "https://github.com/boostorg/boost.git"  
        GIT_TAG boost-1.77.0  
        PATCH_COMMAND cd <SOURCE_DIR>/libs/math && git checkout v1.77-standalone  
)  
FetchContent_MakeAvailable(boost)  
FetchContent_GetProperties(boost SOURCE_DIR BOOST_INCLUDE_DIR)  
  
list(POP_BACK CMAKE_MESSAGE_INDENT)  
message("Fetched boost")  
```  
is far superior

---

## Comment 5

> Username: pascalpfeil  
> Created at: 2021-11-01 18:30:03 UTC  
> Url: https://github.com/boostorg/math/issues/708#issuecomment-956482924  

Any news? The fix would be simply updating the boost math submodule to `v1.77-standalone` in https://github.com/boostorg/boost

---

## Comment 6

> Username: NAThompson  
> Created at: 2021-11-03 04:22:21 UTC  
> Url: https://github.com/boostorg/math/issues/708#issuecomment-958646226  

@pascalpfeil : Will the 1.78 release work for you?

---

## Comment 7

> Username: pascalpfeil  
> Created at: 2021-11-03 07:02:02 UTC  
> Updated at: 2021-11-03 10:16:29 UTC  
> Url: https://github.com/boostorg/math/issues/708#issuecomment-958694971  

I think so, 1.78 will include the current Boost Math version, right?

---

## Comment 8

> Username: NAThompson  
> Created at: 2021-11-03 15:01:37 UTC  
> Url: https://github.com/boostorg/math/issues/708#issuecomment-959355747  

Yes.  
  
You will have to forgive us for being complete CMake noobs.

---

## Comment 9

> Username: pascalpfeil  
> Created at: 2021-11-03 15:02:51 UTC  
> Url: https://github.com/boostorg/math/issues/708#issuecomment-959359918  

No worries, I am as well :)

---

## Comment 10

> Username: NAThompson  
> Created at: 2021-11-03 15:04:51 UTC  
> Url: https://github.com/boostorg/math/issues/708#issuecomment-959366784  

Ok, 1.78 beta comes out tomorrow, so perhaps that's a place we can test.  
  
I admit to not understanding the subtleties of this issue since I just use the standalone . . .

---

## Comment 11

> Username: pascalpfeil  
> Created at: 2021-11-03 15:15:46 UTC  
> Url: https://github.com/boostorg/math/issues/708#issuecomment-959403964  

Basically, when I use boost as a dependency and then compile my project I get  
```  
/usr/include/boost/math/common_factor_ct.hpp:17:68: error: _Pragma takes a parenthesized string literal  
   17 | BOOST_MATH_HEADER_DEPRECATED("<boost/integer/common_factor_ct.hpp>");  
      |                                                                    ^  
In file included from /usr/include/boost/math/common_factor_ct.hpp:15,  
                 from test.cpp:1:  
/usr/include/boost/math/common_factor_ct.hpp:17:1: error: ‘_Pragma’ does not name a type  
   17 | BOOST_MATH_HEADER_DEPRECATED("<boost/integer/common_factor_ct.hpp>");  
      | ^~~~~~~~~~~~~~~~~~~~~~~~~~~~  
 ```  
  
This is already fixed in boost math standalone, but as the main boost repo's submodule is still in a old state, I still get these errors when using boost unmodified.

---

## Comment 12

> Username: pascalpfeil  
> Created at: 2021-11-03 15:21:38 UTC  
> Url: https://github.com/boostorg/math/issues/708#issuecomment-959423754  

Actually, this commit did fix it: https://github.com/boostorg/boost/commit/14664740f577a2c2855e8996efbc05c031cdf8b9
