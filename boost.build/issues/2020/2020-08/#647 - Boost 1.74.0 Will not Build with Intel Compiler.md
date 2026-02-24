# #647 - Boost 1.74.0 Will not Build with Intel Compiler [Closed]

> Username: BryanFlynt-NOAA  
> Created at: 2020-08-25 16:20:15 UTC  
> Updated at: 2021-12-01 14:26:09 UTC  
> Closed at: 2020-09-04 18:45:54 UTC  
> Url: https://github.com/boostorg/build/issues/647  

There was an old Issue #475 which is labeled as closed but seems to have got back into the code.    
Following those instructions I cannot make it work so something else is going on.  
  
I've tried the following versions of Intel with the same results:  
- 18.0.5.274  
- 19.0.5.281  
- 2020  
  
Linux Terminal Commands:  
-----------------------------------------------------  
> cd boost_1_74_0  
> ./bootstrap.sh --with-toolset=intel-linux  
Building Boost.Build engine with toolset intel-linux...   
Failed to build Boost.Build build engine  
Consult 'bootstrap.log' for more details    
  
> head bootstrap.log  
A C++11 capable compiler is required for building the B2 engine.  
Toolset 'intel-linux' does not appear to support C++11.  
  
** Note, the C++11 capable compiler is _only_ required for building the B2  
** engine. The B2 build system allows for using any C++ level and any other  
** supported language and resource in your projects.

---

## Comment 1

> Username: sekoenig  
> Created at: 2020-09-04 18:41:32 UTC  
> Url: https://github.com/boostorg/build/issues/647#issuecomment-687317260  

I just encountered the same issue with Boost 1.73.0.  I found it can be solved by editing `tools/build/src/engine/build.sh`. In the function `test_cxx11()` the `intel-linux` case should pass `-std=c++11`, and perhaps better use `icpc` instead of `icc`.  
  
That fixes the initial problem, analogous fixes should be implemented when setting `CXX` and `B2_CXX` in the same file.  
  
With those, boostrapping works for me.

---

## Comment 2

> Username: grafikrobot  
> Created at: 2020-09-04 18:45:54 UTC  
> Url: https://github.com/boostorg/build/issues/647#issuecomment-687319246  

This was fixed with https://github.com/boostorg/build/commit/86271a42865b2d6ebf4440549e9df1ccc05e9d13

---

## Comment 3

> Username: sekoenig  
> Created at: 2020-09-04 20:32:10 UTC  
> Url: https://github.com/boostorg/build/issues/647#issuecomment-687368157  

Ah, great -- thanks!

---

## Comment 4

> Username: josemunozc  
> Created at: 2021-08-27 13:40:01 UTC  
> Url: https://github.com/boostorg/build/issues/647#issuecomment-907211333  

Hi,  
Not sure if related with this issues (I tested the following with v1.77.0) but I notice that in `tools/build/src/engine/build.sh` the function `check_toolset ()` defines TOOLSET at the beginning:   
  
```  
check_toolset ()  
{  
    TOOLSET=${B2_TOOLSET%%-*}  
    TOOLSET_SUFFIX=${B2_TOOLSET##$TOOLSET}  
```  
  
If `B2_TOOLSET` is set to intel-linux then `TOOLSET` will be set to intel. This will cause intel tests to fail?  
  
```  
    # Intel Pro (intel-linux)  
    if test_toolset intel-linux && test_path icpc ; then  
        if test_compiler icpc -x c++ -std=c++11 ; then B2_TOOLSET=intel-linux ; return ${TRUE} ; fi  
    fi  
  
```  
  
since `test_toolset intel-linux` will always be false (return 1):  
  
```  
test_toolset ()  
{  
    if test "${TOOLSET}" = "" ; then return ${TRUE} ; fi  
    if test "${TOOLSET}" = "$1" -o "${TOOLSET}" = "$2" -o "${TOOLSET}" = "$3" ; then return ${TRUE} ; fi  
    return 1  
}  
```  
  
but perhaps I failed to follow the logic properly.

---

## Comment 5

> Username: boegel  
> Created at: 2021-12-01 14:26:09 UTC  
> Url: https://github.com/boostorg/build/issues/647#issuecomment-983693674  

@zerpiko I ran into the same problem when trying to build Boost 1.77.0 with Intel compilers (2021 update 4), I worked around it by replacing `${TOOLSET}` with `${B2_TOOLSET}` in `test_toolset`, by running the following command before running the `bootstrap.sh` script:  
  
```shell  
sed -i 's/{TOOLSET}/{B2_TOOLSET}/g' tools/build/src/engine/build.sh  
```
