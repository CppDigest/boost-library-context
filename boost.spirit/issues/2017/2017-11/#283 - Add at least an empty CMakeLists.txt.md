# #283 - Add at least an empty CMakeLists.txt [Closed]

> Username: mwpowellhtx  
> Created at: 2017-11-19 18:30:12 UTC  
> Updated at: 2017-11-27 16:49:25 UTC  
> Closed at: 2017-11-19 18:48:55 UTC  
> Url: https://github.com/boostorg/spirit/issues/283  

Hello,  
  
I am trying to integrate Boost/Spirit  (minimum) into a *CMake* based project via ``ExternalProject_Add``, and I am receiving the following error:  
  
```  
Severity	Code	Description	Project	File	Line	Suppression State  
Error		The source directory "path/to/repos/boost/spirit" does not appear to contain CMakeLists.txt.	download_boost_spirit	path\to\build\CUSTOMBUILD	1	  
```  
  
Not sure if/where Boost (or Spirit) stand WRT CMake, but would you at least add an empty ``CMakeLists.txt`` file? Probably could be a no-op, or at minimum report Spirit as header only and nominal getting started instructions.  
  
This is from CMake for Windows, building via Microsoft Visual Studio 2015.  
  
Thanks!

---

## Comment 1

> Username: Kojoley  
> Created at: 2017-11-19 18:48:55 UTC  
> Url: https://github.com/boostorg/spirit/issues/283#issuecomment-345539939  

CMake has native support for Boost https://cmake.org/cmake/help/v3.8/module/FindBoost.html. Your should not use `ExternalProject_Add`.  
  
```cmake  
find_package(Boost REQUIRED)  
#find_package(Boost COMPONENTS system REQUIRED) # if you are going to link Boost.System  
include_directories(BEFORE SYSTEM ${Boost_INCLUDE_DIRS})  
target_link_libraries(${PROJECT_NAME} ${Boost_LIBRARIES})  
```  
  
I cannot say what is the minimal set of Boost libraries Spirit requires, but you can try to grep the sources to figure it out. Google should help you to find out how to setup a modular Boost installation.  
  
Feel free to reopen the issue if did not answer you question.

---

## Comment 2

> Username: mwpowellhtx  
> Created at: 2017-11-19 19:15:21 UTC  
> Url: https://github.com/boostorg/spirit/issues/283#issuecomment-345541739  

@Kojoley Yes, I know I can find the whole Boost library, but this is the whole point of this issue. I neither want nor require the whole Boost dependency. That is way overkill for what I am doing. At minimum, just the Spirit library, and nominal dependencies.

---

## Comment 3

> Username: Kojoley  
> Created at: 2017-11-19 19:57:30 UTC  
> Url: https://github.com/boostorg/spirit/issues/283#issuecomment-345544645  

Spirit is header-only library, you do not need to build it. `ExternalProject_Add` documentation states that you need a `CMakeLists.txt` when no `CONFIGURE_COMMAND` option is specified.  
  
> I know I can find the whole Boost library, but this is the whole point of this issue. I neither want nor require the whole Boost dependency. At minimum, just the Spirit library, and nominal dependencies.  
  
You do not need the whole Boost, or even to build it, but Spirit heavily uses Boost, directly or indirectly, the some subset of Boost have to be on your compiler search paths. You can minimize your Boost installation (by manually removing libraries, or using modular Boost), but there is (unfortunately) no dependency tree.  
Suggestion: Try to fork [boost super-project](https://github.com/boostorg/boost), leave only required libraries, add it to your build script and use with FindBoost.

---

## Comment 4

> Username: mwpowellhtx  
> Created at: 2017-11-19 20:30:57 UTC  
> Url: https://github.com/boostorg/spirit/issues/283#issuecomment-345547088  

@Kojoley Much appreciated follow up. Hmm, that is good to know. I'll try with the CMake commands. The thing I want to avoid are any targets that are too aggressive in their stance on updating/rebuilding. I may look into that.

---

## Comment 5

> Username: Kojoley  
> Created at: 2017-11-27 13:23:49 UTC  
> Url: https://github.com/boostorg/spirit/issues/283#issuecomment-347180086  

You might be interested in:  
  - List of dependencies: https://pdimov.github.io/boostdep-report/develop/module-overview.html  
  - The bcp utility: http://www.boost.org/doc/libs/1_65_1/tools/bcp/doc/html/index.html

---

## Comment 6

> Username: mwpowellhtx  
> Created at: 2017-11-27 16:49:25 UTC  
> Url: https://github.com/boostorg/spirit/issues/283#issuecomment-347244609  

@Kojoley Indeed, thank you very much. :+1:
