# #872 CMake for GNU/Linux [Closed]

> Username: ajneu  
> Created at: 2017-11-06 15:35:44 UTC  
> Updated at: 2017-11-22 21:13:52 UTC  
> Closed at: 2017-11-22 21:13:52 UTC  
> Url: https://github.com/boostorg/beast/pull/872  

Some changes to get it to run under Linux, with the following command  
  
```bash  
mkdir build  
cd    build  
cmake -DBOOST_ROOT=~/development/boost_own/boost_new_asio/ ..  
make -j8  
```  
  
where I've compiled Boost myself and the following paths are on my system:  
```bash  
~/development/boost_own/boost_new_asio/include/boost/ # has e.g. asio/ etc.  
~/development/boost_own/boost_new_asio/lib/           # has e.g. libboost_system.a libboost_system.so libboost_system.so.1.65.0  
```  
  
I was not able to check behaviour on Visual Studio, as I don't have windows.  
Some changes might be necessary, to accomodate these changes and Visual Studio, but I cannot test it.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2017-11-06 15:56:47 UTC  
> Updated_at: 2017-11-22 13:56:03 UTC  
> Url: https://github.com/boostorg/beast/pull/872#issuecomment-342192816  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/872?src=pr&el=h1) Report  
> Merging [#872](https://codecov.io/gh/boostorg/beast/pull/872?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/6e6e1f6fbc93b92c8b21d673933e3b77951b4fa0?src=pr&el=desc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/872/graphs/tree.svg?width=650&src=pr&token=Gq6MFA9JRF&height=150)](https://codecov.io/gh/boostorg/beast/pull/872?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #872   +/-   ##  
========================================  
  Coverage    95.69%   95.69%             
========================================  
  Files          104      104             
  Lines        10454    10454             
========================================  
  Hits         10004    10004             
  Misses         450      450  
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/872?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/872?src=pr&el=footer). Last update [6e6e1f6...21b58a8](https://codecov.io/gh/boostorg/beast/pull/872?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 2

> Username: vinniefalco  
> Created_at: 2017-11-06 16:23:01 UTC  
> Updated_at: 2017-11-06 16:29:55 UTC  
> Url: https://github.com/boostorg/beast/pull/872#issuecomment-342201748  

>`find_package(Boost COMPONENTS system coroutine filesystem)`  
  
`find_package` doesn't work when Beast is built "in-tree." The workflow for building tests and examples is to clone the Boost superproject, `cd libs/beast`, and run CMake from there. It will find the headers and libraries relative to that directory. This is the only way to sanely work on multiple versions of Boost at the same time.  
  
Your solution will only work for the "installed" Boost which of course breaks in-tree builds. This is a general problem of combining Boost and CMake which we hope to resolve generally for all Boost libraries. But that could take a long time.  
  
Also, CMake is always behind the latest version of Boost when it comes to `find_package`. This is a source of endless frustration for both users and developers.  
  
Until then, Beast's CMake support is limited to Windows, which supports "automatic linking."

---

## Comment 3

> Username: vinniefalco  
> Created_at: 2017-11-06 16:25:45 UTC  
> Url: https://github.com/boostorg/beast/pull/872#issuecomment-342202685  

Here is the workflow that your proposed change would break:  
```  
git clone git@github.com:boostorg/boost.git  
cd boost  
git submodule init  
git submodule update  
./bootstrap.sh  
b2 variant=debug,release link=static runtime-link=static  
cd libs/beast  
mkdir bin  
cd bin  
cmake ..  
```  
  
After running `cmake` above, the resulting Makefile (or Visual Studio project file) will point to the system's "installed" version of boost (something like /usr/lib/boost/### and /usr/include/boost/###) instead of the version of boost clone in the tree above. This of course produces the wrong results.

---

## Comment 4

> Username: ajneu  
> Created_at: 2017-11-06 23:50:39 UTC  
> Url: https://github.com/boostorg/beast/pull/872#issuecomment-342327295  

Thanks for explaining.   
  
> Also, CMake is always behind the latest version of Boost when it comes to `find_package`. This is a source of endless frustration for both users and developers.  
  
One could add a new version of boost... by trying this:  
https://gitlab.kitware.com/cmake/cmake/blob/master/Modules/FindBoost.cmake#L525

---
