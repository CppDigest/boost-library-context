# #900 cmake for beast on Linux [Closed]

> Username: ajneu  
> Created at: 2017-11-22 22:43:16 UTC  
> Updated at: 2018-05-01 17:32:32 UTC  
> Closed at: 2018-05-01 17:32:32 UTC  
> Url: https://github.com/boostorg/beast/pull/900  

_Update: this comment is for the 1st commit of this pull-request_  
  
... new attempt at #872 ...  
  
Hi!  
  
Regarding your [comment](https://github.com/boostorg/beast/pull/872#issuecomment-342202685),  
the described workflow does not work for me.  
  
I spent some time on it... and here's a way that will work under linux:  
  
See [below](https://github.com/boostorg/beast/pull/900#issuecomment-346689759)

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2017-11-22 23:25:15 UTC  
> Updated_at: 2017-11-23 16:19:14 UTC  
> Url: https://github.com/boostorg/beast/pull/900#issuecomment-346499598  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/900?src=pr&el=h1) Report  
> Merging [#900](https://codecov.io/gh/boostorg/beast/pull/900?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/6e6e1f6fbc93b92c8b21d673933e3b77951b4fa0?src=pr&el=desc) will **decrease** coverage by `<.01%`.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/900/graphs/tree.svg?height=150&width=650&src=pr&token=Gq6MFA9JRF)](https://codecov.io/gh/boostorg/beast/pull/900?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #900      +/-   ##  
===========================================  
- Coverage    95.69%   95.68%   -0.01%       
===========================================  
  Files          104      104                
  Lines        10454    10454                
===========================================  
- Hits         10004    10003       -1       
- Misses         450      451       +1  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/900?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/websocket/impl/close.ipp](https://codecov.io/gh/boostorg/beast/pull/900?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC93ZWJzb2NrZXQvaW1wbC9jbG9zZS5pcHA=) | `99.02% <0%> (-0.49%)` | :arrow_down: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/900?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/900?src=pr&el=footer). Last update [6e6e1f6...7346aae](https://codecov.io/gh/boostorg/beast/pull/900?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: ajneu  
> Created_at: 2017-11-23 15:38:30 UTC  
> Url: https://github.com/boostorg/beast/pull/900#issuecomment-346648937  

Just added a new commit above!  
The links in the top comment are no longer valid.

---

## Comment 3

> Username: ajneu  
> Created_at: 2017-11-23 15:40:51 UTC  
> Url: https://github.com/boostorg/beast/pull/900#issuecomment-346649483  

Now, if cmake's default `FindBoost.cmake` can find the Boost version and dependencies, then all is good.  
Only if this is not the case, cmake automatically tries a tweaked `FindBoost.cmake`:  
  
[Link](https://github.com/ajneu/beast/blob/develop/CMakeLists.txt#L78)

---

## Comment 4

> Username: vinniefalco  
> Created_at: 2017-11-23 15:50:01 UTC  
> Url: https://github.com/boostorg/beast/pull/900#issuecomment-346651708  

I can't merge these changes. I don't understand it, Boost does not support CMake, and editing the FindBoost.cmake is not something that I want to advise users to do. We can't do anything like this until Boost has some sort of official CMake support. Sorry about it.

---

## Comment 5

> Username: ajneu  
> Created_at: 2017-11-23 19:54:08 UTC  
> Updated_at: 2017-11-24 10:40:55 UTC  
> Url: https://github.com/boostorg/beast/pull/900#issuecomment-346689759  

Allow me to explain it.  
  
Here is a complete workflow to work with boost and beast and cmake:  
  
```bash  
git clone https://github.com/boostorg/boost.git  
cd boost  
git submodule init  
git submodule update  
  
NUM_CORES=$(grep -c ^processor /proc/cpuinfo)  
  
./bootstrap.sh # creates: b2 bjam bootstrap.log project-config.jam	  
  
rm -rf                  bin.v2  boost  
./b2 headers # creates: bin.v2  boost  
  
BUILD_DIR=$(pwd)/TMP_BUILD/BUILD_DIR  
STAGE_DIR=$(pwd)/TMP_BUILD/STAGE_DIR  
BOOST_INSTALL_DIR=$(pwd)/BOOST_INSTALL_DIR  
  
  
WITH_LIBS=" --with-system --with-filesystem --with-coroutine --with-context --with-thread --with-chrono --with-date_time --with-atomic "  # to build all libs, use: WITH_LIBS=""  
./b2 -j$NUM_CORES --build-dir="${BUILD_DIR}" --stagedir="${STAGE_DIR}" $WITH_LIBS stage # -d+2 `#verbose`                  # creates BUILD_DIR and STAGE_DIR  
  
  
rm -rf                                                                                                                    $BOOST_INSTALL_DIR  
./b2 -j$NUM_CORES --build-dir="${BUILD_DIR}" --stagedir="${STAGE_DIR}" $WITH_LIBS --prefix="${BOOST_INSTALL_DIR}" install  # creates $BOOST_INSTALL_DIR  
  
cd libs/beast  
if ! grep "system coroutine filesystem" CMakeLists.txt &>/dev/null; then  
  git remote add ajneu https://github.com/ajneu/beast.git  
  git fetch      ajneu  
  git checkout  -B ajneu_develop  ajneu/develop  
fi  
mkdir -p bin  
cd       bin  
cmake -DBOOST_ROOT=$BOOST_INSTALL_DIR ..  
cmake --build . --target all -- -j$NUM_CORES VERBOSE=1  
```  
  
It works by first cloning boost, and then building all required libraries (system, filesystem, coroutine -- and their dependencies: context thread chrono date_time atomic) and installing them.  
  
Then we go to beast, and set up the correct branch (in the example above, you get my branch [equiv with this pull-request], by adding ajneu as remote, etc.).  
  
Then we build with cmake as usual.  
  
How does it work?  
`FindBoost.cmake` needs to know the dependencies of system, filesystem, coroutine.  
But it only knows these dependencies for older versions of boost, where version < 1.66.0 :  [Link](https://gitlab.kitware.com/cmake/cmake/blob/v3.10.0/Modules/FindBoost.cmake#L783).  
For boost version >= 1.66.0, it fails: [Link](https://gitlab.kitware.com/cmake/cmake/blob/v3.10.0/Modules/FindBoost.cmake#L800).  
  
So all we must do is: detect if cmake's boost-detection fails, and then use a tweaked `FindBoost.cmake`, where the following lines ([Link](https://gitlab.kitware.com/cmake/cmake/blob/v3.10.0/Modules/FindBoost.cmake#L800)), are modified as follows:  
  
```diff  
801,802c801,822  
<       message(WARNING "New Boost version may have incorrect or missing dependencies and imported targets")  
<       set(_Boost_IMPORTED_TARGETS FALSE)  
---  
>       #message(WARNING "New Boost version may have incorrect or missing dependencies and imported targets")  
>       #set(_Boost_IMPORTED_TARGETS FALSE)  
>       #special tweak for Boost  
>       if (NOT TWEAK_BOOST)  
>         set(TWEAK_BOOST TRUE CACHE BOOL "")  
>         message("==> Using tweaked FindBoost.cmake: ${CMAKE_CURRENT_LIST_FILE}")  
>       endif()  
>       set(_Boost_CONTEXT_DEPENDENCIES thread chrono system date_time)  
>       set(_Boost_COROUTINE_DEPENDENCIES context system)  
>       set(_Boost_FIBER_DEPENDENCIES context thread chrono system date_time)  
>       set(_Boost_FILESYSTEM_DEPENDENCIES system)  
>       set(_Boost_IOSTREAMS_DEPENDENCIES regex)  
>       set(_Boost_LOG_DEPENDENCIES date_time log_    setup system filesystem thread regex chrono atomic)  
>       set(_Boost_MATH_DEPENDENCIES math_c99 math_c99f math_c99l math_tr1 math_tr1f math_tr1l atomic)  
>       set(_Boost_MPI_DEPENDENCIES serialization)  
>       set(_Boost_MPI_PYTHON_DEPENDENCIES python mpi serialization)  
>       set(_Boost_NUMPY_DEPENDENCIES python)  
>       set(_Boost_RANDOM_DEPENDENCIES system)  
>       set(_Boost_THREAD_DEPENDENCIES chrono system date_time atomic)  
>       set(_Boost_WAVE_DEPENDENCIES filesystem system serialization thread chrono date_time atomic)  
>       set(_Boost_WSERIALIZATION_DEPENDENCIES serialization)  
>         
```  
  
This is accomplished here:  
* Try normal `find_package(Boost COMPONENTS system coroutine filesystem)`: [Line 76](https://github.com/ajneu/beast/blob/develop/CMakeLists.txt#L76)  
* If fails: [Line 78](https://github.com/ajneu/beast/blob/develop/CMakeLists.txt#L78)  
* Then modify module search-path: [Line 81](https://github.com/ajneu/beast/blob/develop/CMakeLists.txt#L81) (to find tweaked FindBoost.cmake at [cmake_module/FindBoost.cmake](https://github.com/ajneu/beast/blob/develop/cmake_module/FindBoost.cmake))  
* And rerun find_package: [Line 82](https://github.com/ajneu/beast/blob/develop/CMakeLists.txt#L82)  
  
PS:  
The dependencies (shown in the diff above) will remain pretty stable, for the libraries you are using: system, coroutine, filesystem.   
To determine the dependencies use:  
  
```bash  
cd /tmp  
mkdir cmake_tmp  
cd    cmake_tmp  
wget https://gitlab.kitware.com/cmake/cmake/raw/release/Utilities/Scripts/BoostScanDeps.cmake  
  
cd /tmp  
git clone https://github.com/boostorg/boost.git  
cd boost  
git submodule init  
git submodule update  
./bootstrap.sh # creates: b2 bjam bootstrap.log project-config.jam	  
rm -rf                  bin.v2  boost  
./b2 headers # creates: bin.v2  boost  
mkdir -p BOOST_INSTALL/include  
cp -rL boost BOOST_INSTALL/include  
  
cmake -DBOOST_DIR=/tmp/boost/BOOST_INSTALL/include -P /tmp/cmake_tmp/BoostScanDeps.cmake  
```

---

## Comment 6

> Username: ajneu  
> Created_at: 2017-11-23 20:47:38 UTC  
> Url: https://github.com/boostorg/beast/pull/900#issuecomment-346695518  

> Boost does not support CMake  
  
Correct. But CMake has Boost support  
  
> and editing the FindBoost.cmake is not something that I want to advise users to do.  
  
Correct; they shouldn't. That's why we should provide a tweaked `FindBoost.cmake` for them.  
  
> We can't do anything like this until Boost has some sort of official CMake support.  
  
Disagree. Boost has no CMake support: You need to build the boost libs using `b2`. But once that is finished, we can use CMake, to locate the libs and build projects, that depend on those libs.  
The pull-request shows it for beast.  
  
.  
.  
  
By the way: as it stands now, beast's current CMakeLists.txt are in error, since they are missing `target_link_library` information such as [this](https://github.com/ajneu/beast/blob/develop/example/advanced/server/CMakeLists.txt#L20)

---

## Comment 7

> Username: vinniefalco  
> Created_at: 2017-11-24 11:47:03 UTC  
> Url: https://github.com/boostorg/beast/pull/900#issuecomment-346811067  

> beast's current CMakeLists.txt are in error  
  
Beast's CMake is clearly marked as working for Windows only, where autolinking is supported

---

## Comment 8

> Username: stale[bot]  
> Created_at: 2018-04-24 16:52:19 UTC  
> Url: https://github.com/boostorg/beast/pull/900#issuecomment-384003472  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 9

> Username: stale[bot]  
> Created_at: 2018-05-01 17:32:26 UTC  
> Url: https://github.com/boostorg/beast/pull/900#issuecomment-385733360  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!

---
