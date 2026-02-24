# #244 - cmake errors out [Closed]

> Username: mark-99  
> Created at: 2022-07-25 15:22:05 UTC  
> Updated at: 2022-10-18 11:20:59 UTC  
> Closed at: 2022-07-28 17:02:02 UTC  
> Url: https://github.com/boostorg/url/issues/244  

I'm building a project with Boost 1.79 via vcpkg.  
Since Boost.URL isn't in vcpkg I'm using CPM.cmake (a wrapper over FetchContent) which is used successfully for various other things.  
There's no version or tag so I'm using a hash directly like so:  
```  
CPMAddPackage("gh:CPPAlliance/url#aecf3a621805c764017dfb92a79008cb74f1229c")  
```  
Note I haven't added anything else to cmake as yet.  
It downloads ok:  
```  
[CMake] -- CPM: adding package url@0 (aecf3a621805c764017dfb92a79008cb74f1229c to C:/Temp/cpm-source-cache/url/f99d17f730b235622d8217f31f9c10de78e58446)  
```  
However later it errors out:  
```  
1> [CMake] -- Found Boost: <...>/out/build/x64-debug-clang/vcpkg_installed/x64-windows-cpp20/include (found version "1.79.0") found components: coroutine iostreams thread context regex chrono atomic   
1> [CMake] CMake Error at vcpkg/scripts/buildsystems/vcpkg.cmake:573 (_add_executable):  
1> [CMake]   _add_executable cannot create target "tests" because another target with  
1> [CMake]   the same name already exists.  The existing target is a custom target  
1> [CMake]   created in source directory  
1> [CMake]   "C:/Temp/cpm-source-cache/url/f99d17f730b235622d8217f31f9c10de78e58446/test".  
```  
I'm not sure why it's trying to add a 'test' target in the first place.  
I saw another ticket saying detection of an existing Boost tree was heuristic, so maybe it's that.  
Either way is there anything I can set in cmake to make this work?  
Is there another recommended way to add this repo (short of a copy-paste into our source tree)?  
  
Thx.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-07-25 15:24:39 UTC  
> Updated at: 2022-07-25 15:24:50 UTC  
> Url: https://github.com/boostorg/url/issues/244#issuecomment-1194197343  

put the url directory in `${BOOST}/libs` and then from the command line run `b2 headers` in `${BOOST}` to create the symlinks. Or you can manually add `${BOOST}/libs/url/include` to your projects set of include paths (`-I` is the compiler switch I believe).

---

## Comment 2

> Username: vinniefalco  
> Created at: 2022-07-25 15:25:19 UTC  
> Url: https://github.com/boostorg/url/issues/244#issuecomment-1194198172  

This is where I put mine:  
![image](https://user-images.githubusercontent.com/1503976/180815828-67e80a00-8abc-463f-a2ac-aca1dfb4269e.png)

---

## Comment 3

> Username: alandefreitas  
> Created at: 2022-07-25 15:30:34 UTC  
> Url: https://github.com/boostorg/url/issues/244#issuecomment-1194208583  

@mark-99   
  
I believe https://github.com/CPPAlliance/url/pull/236 fixes your problem.

---

## Comment 4

> Username: mark-99  
> Created at: 2022-07-25 16:41:58 UTC  
> Url: https://github.com/boostorg/url/issues/244#issuecomment-1194334407  

Putting URL into Boost tree isn't really an option - Boost is installed by vcpkg in an opaque cached location (which can be cleaned).  
  
#236 was tricky to try out as it's not merged, but I manually copy-pasted over the CMakeLists.txt, but still gives the same error.

---

## Comment 5

> Username: alandefreitas  
> Created at: 2022-07-25 17:10:19 UTC  
> Updated at: 2022-07-25 17:12:21 UTC  
> Url: https://github.com/boostorg/url/issues/244#issuecomment-1194366880  

> Putting URL into Boost tree isn't really an option - Boost is installed by vcpkg in an opaque cached location (which can be cleaned).  
  
Yes. Patching boost is not always easy.  
  
> https://github.com/CPPAlliance/url/pull/236 was tricky to try out as it's not merged,   
  
```bash  
git clone --branch cmake_root https://github.com/alandefreitas/url.git  
```  
  
or (IIRC)  
  
```cmake  
CPMAddPackage("alandefreitas/url#cmake_root")  
```  
  
> but I manually copy-pasted over the CMakeLists.txt, but still gives the same error.  
  
According to your error, the script is trying to create two `tests` targets. If you look into `url/test/CMakelists.txt`, you'll see that  Boost.URL won't create this target if it's already defined. And if you look at your error, it's trying to create this target in `vcpkg.cmake`, not `url/test/CMakelists.txt`.  
  
This means your script is trying to create a `tests` target _after_ Boost.URL was included. But I don't have enough information to tell you where. In any case, you have two solutions:  
  
1) Include Boost.URL after the first test target has been created. I don't know how hard this is because I don't know where it's being created.  
2) Include Boost.URL at any point and not let Boost.URL create the test target. You just need to set the option `BOOST_URL_BUILD_TESTS` to `OFF`.  
  
In CMake  
  
```cmake  
set(BOOST_URL_BUILD_TESTS OFF CACHE BOOL "")  
CPMAddPackage("alandefreitas/url#cmake_root")  
```  
  
In CPM:  
  
```cmake  
CPMAddPackage(  
  NAME              boost_url  
  VERSION           cmake_root  
  GITHUB_REPOSITORY alandefreitas/url  
  OPTIONS  
      "BOOST_URL_BUILD_TESTS OFF"  
)  
```  
  
By the way, this helped me notice the `tests` target is not namespaced and therefore ambiguous. This creates problems even though it won't recreate the target because the target might be created later. I don't know if this name is just a coincidence or purposeful.

---

## Comment 6

> Username: mark-99  
> Created at: 2022-07-25 19:14:22 UTC  
> Url: https://github.com/boostorg/url/issues/244#issuecomment-1194506651  

It looked to me from the logs like the clash was with Boost itself.  
The ordering isn't under my control, as vcpkg runs first as a 'toolchain'.  
I do have a target called 'tests', but my understanding is that'll be namespaced to my project() (and removing it doesn't change anything).  
```  
set(BOOST_URL_BUILD_TESTS OFF CACHE BOOL "")  
CPMAddPackage("alandefreitas/url#cmake_root")  
```  
gives this error:  
```  
1> [CMake] CMake Error at C:/Temp/cpm-source-cache/cpm/CPM_0.35.0.cmake:345 (message):  
1> [CMake]   CPM: Can't determine package type of 'alandefreitas/url#cmake_root'  
1> [CMake] Call Stack (most recent call first):  
1> [CMake]   C:/Temp/cpm-source-cache/cpm/CPM_0.35.0.cmake:440 (cpm_parse_add_package_single_arg)  
```

---

## Comment 7

> Username: alandefreitas  
> Created at: 2022-07-25 19:41:25 UTC  
> Url: https://github.com/boostorg/url/issues/244#issuecomment-1194538263  

I think this is missing the "gh:" prefix  
  
```cmake  
CPMAddPackage("gh:alandefreitas/url#cmake_root")  
```  
  
or just try the other CPMAddPackage overload  
  
```  
CPMAddPackage(  
  NAME              boost_url  
  VERSION           cmake_root  
  GITHUB_REPOSITORY alandefreitas/url  
  OPTIONS  
      "BOOST_URL_BUILD_TESTS OFF"  
)  
```

---

## Comment 8

> Username: mark-99  
> Created at: 2022-07-25 21:58:11 UTC  
> Url: https://github.com/boostorg/url/issues/244#issuecomment-1194682368  

Gah, sorry of course - fixed. I also renamed my own "tests" target to something with a prefix to get that out of the way (as that was conflicting also, I guess because of the missing namespace you mentioned).  
It gets past 'configuring' stage now, then a bunch of these:  
```  
1> [CMake] -- Configuring done  
1> [CMake] CMake Error at vcpkg/scripts/buildsystems/vcpkg.cmake:573 (_add_executable):  
1> [CMake]   Target "boost_url_limits" links to target "Boost::align" but the target was  
1> [CMake]   not found.  Perhaps a find_package() call is missing for an IMPORTED  
1> [CMake]   target, or an ALIAS target is missing?  
1> [CMake] Call Stack (most recent call first):  
  
<similarly config, core, type_traits, system, variant2>  
  
1> [CMake]   C:/Temp/cpm-source-cache/url/48c40181141f1a07fa89480a101e059b7e0a4fdf/test/limits/CMakeLists.txt:15 (add_executable)  
1> [CMake] -- Generating done  
1> [CMake] CMake Generate step failed.  Build files cannot be regenerated correctly.  
```  
Note I have added the named boost packages to vcpkg so they are all installed now, usually for header-only that's all you need to do (for link-time ones you have to list them in target_link_libraries() also). Like so:  
```  
find_package(Boost COMPONENTS coroutine iostreams thread REQUIRED)  
target_link_libraries(${TARGET} PRIVATE ${Boost_LIBRARIES})  
```  
I'm not clear why the library is attempting to add an executable - I really just want the headers.  
Well it's getting further at least :)

---

## Comment 9

> Username: alandefreitas  
> Created at: 2022-07-26 02:20:45 UTC  
> Url: https://github.com/boostorg/url/issues/244#issuecomment-1194898539  

What you get now is:  
  
`Target "boost_url_limits" links to target "Boost::align" but the target was not found`  
  
So:  
  
1) `boost_url_limits` is a test, so it means you haven't disabled the tests yet with `BOOST_URL_BUILD_TESTS`  
2) The build script [assumes](https://github.com/alandefreitas/url/blob/59870b3f3e3f59dba4437291c039b5a87afb460f/CMakeLists.txt#L50-L58) `find_package(Boost)` is going to find Boost. Manually listing and calling find_package for each transitive dependency is unfeasible. So if you are getting individual libraries from vcpkg (not the complete boost package), it's not that easy to make it work:  
    - You'll need to at least download all the boost modules Boost.URL depends on.   
    - And then get passed [that](https://github.com/alandefreitas/url/blob/59870b3f3e3f59dba4437291c039b5a87afb460f/CMakeLists.txt#L50-L58).   
    - It might easier to just download the thing and not include the subdirectory. In CPMAddPackage, that would be the DOWNLOAD only option. Then you just call `target_include_directories` with your target and the Boost.URL include directory.

---

## Comment 10

> Username: mark-99  
> Created at: 2022-07-26 08:15:17 UTC  
> Updated at: 2022-07-26 08:15:38 UTC  
> Url: https://github.com/boostorg/url/issues/244#issuecomment-1195156582  

1. I'm doing this:  
```  
set(BOOST_URL_BUILD_TESTS OFF CACHE BOOL "")  
CPMAddPackage("gh:alandefreitas/url#cmake_root")  
```  
2. All the dependent modules are downloaded. Here is that part of my manifest:  
```  
    "dependencies": [  
        "boost-align",  
        "boost-assert",  
        "boost-beast",  
        {  
            "name": "boost-asio",  
            "default-features": false,  
            "features": [ "ssl" ]  
        },  
        "boost-circular-buffer",  
        "boost-config",  
        "boost-conversion",  
        "boost-core",  
        "boost-describe",  
        "boost-interprocess",  
        "boost-iostreams",  
        "boost-mp11",  
        "boost-predef",  
        "boost-stacktrace",  
        "boost-static-string",  
        "boost-system",  
        "boost-thread",  
        "boost-type-traits",  
        "boost-variant2",  
(etc...)  
```  
`find_package(Boost)` works elsewhere, here is an excerpt from my main CMakeLists.txt:  
```  
find_package(Boost COMPONENTS coroutine iostreams thread REQUIRED)  
target_link_libraries(${TARGET} PRIVATE ${Boost_LIBRARIES})  
```  
> DOWNLOAD only option  
  
I'll give that a try.

---

## Comment 11

> Username: mark-99  
> Created at: 2022-07-26 11:14:39 UTC  
> Url: https://github.com/boostorg/url/issues/244#issuecomment-1195348351  

So on the latter option, changing VERSION to GIT_TAG so it doesn't prepend 'v' and this works:  
```  
CPMAddPackage(  
  NAME              boost_url  
  GIT_TAG           cmake_root  
  GITHUB_REPOSITORY alandefreitas/url  
  OPTIONS          "BOOST_URL_BUILD_TESTS OFF"  
  DOWNLOAD_ONLY  
)  
```  
However haven't figured out how to actually use it from there:  
```  
find_package(boost_url CONFIG REQUIRED)  
```  
yields  
```  
1> [CMake] CMake Error at vcpkg/scripts/buildsystems/vcpkg.cmake:824 (_find_package):  
1> [CMake]   Could not find a package configuration file provided by "boost_url" with  
1> [CMake]   any of the following names:  
1> [CMake]   
1> [CMake]     boost_urlConfig.cmake  
1> [CMake]     boost_url-config.cmake  
1> [CMake]   
1> [CMake]   Add the installation prefix of "boost_url" to CMAKE_PREFIX_PATH or set  
1> [CMake]   "boost_url_DIR" to a directory containing one of the above files.  If  
1> [CMake]   "boost_url" provides a separate development package or SDK, be sure it has  
1> [CMake]   been installed.  
```  
just doing  
```  
find_package(boost_url REQUIRED)  
```  
doesn't complain but doesn't seem to do much, in particular `${BOOST_URL_HEADERS}` isn't set so there's nothing to pass to `target_include_directories`

---

## Comment 12

> Username: alandefreitas  
> Created at: 2022-07-27 19:31:28 UTC  
> Url: https://github.com/boostorg/url/issues/244#issuecomment-1197277007  

You don't anything after `CPMAddPackage`.  
  
```cmake  
# Add package  
CPMAddPackage(  
    # boost.url info  
)  
  
# Use package  
add_executable(my_target main.cpp)  
target_link_libraries(my_target PRIVATE boost_url)  
```  
  
`CPMAddPackage` is _not_ something you do before `find_package`. `CPMAddPackage` is an _alternative_ to `find_package` (with pros and cons).

---

## Comment 13

> Username: mark-99  
> Created at: 2022-07-27 21:27:08 UTC  
> Updated at: 2022-07-27 21:30:04 UTC  
> Url: https://github.com/boostorg/url/issues/244#issuecomment-1197386923  

ok, TIL. You do need `find_package` for things installed via vcpkg, so I just left it when I migrated some libraries to CPM, and it didn't do any harm (if apparently wrong/redundant).  
  
Also I figured it would be needed when using the `DOWNLOAD_ONLY`... otherwise is anything actually pulling it into cmake?  
  
Anyway, I removed that and now it looks like this:  
```  
set(BOOST_URL_BUILD_TESTS OFF CACHE BOOL "")  
CPMAddPackage("gh:alandefreitas/url#cmake_root")  
  
target_link_libraries(${TARGET} PRIVATE boost_url)  
```  
and still getting this error:  
```  
1> [CMake]   Target "boost_url_limits" links to target "Boost::align" but the target was  
1> [CMake]   not found.  Perhaps a find_package() call is missing for an IMPORTED  
1> [CMake]   target, or an ALIAS target is missing?  
```  
(and then for config, core, system, type_traits, variant2).  
  
You said `"boost_url_limits` was a test and that flag is supposed to switch tests off, but seems like that isn't working.  
Here is the CMakeCache.txt:  
```  
//Build boost::url examples  
BOOST_URL_BUILD_EXAMPLES:BOOL=OFF  
  
//Build boost::url tests  
BOOST_URL_BUILD_TESTS:BOOL=ON  
  
//Install boost::url files  
BOOST_URL_INSTALL:BOOL=ON  
```  
If I manually change the flag:  
```  
//Build boost::url tests  
BOOST_URL_BUILD_TESTS:BOOL=OFF  
```  
then it builds ok, I can `#include <boost/url.hpp>` (albeit with some warnings):  
```  
C:\Temp\cpm-source-cache\url\48c40181141f1a07fa89480a101e059b7e0a4fdf\include\boost\url\grammar\detail\charset.hpp(136,39): warning : use of old-style cast [-Wold-style-cast]  
          __m128i r2 = _mm_loadu_si128( (__m128i const*)r );  
```  
So I guess the question now is why is that `set()` not overriding the cache var.  
In case it's not obvious I'm not a cmake expert, but after some googling I tried `FORCE`:  
```  
set(BOOST_URL_BUILD_TESTS OFF CACHE BOOL "" FORCE)  
```  
and that works. (If that's not the right thing to do let me know).  
  
Out of interest I also went back to the main repo:  
```  
CPMAddPackage("gh:CPPAlliance/url#aecf3a621805c764017dfb92a79008cb74f1229c")  
```  
and that also works.  
So it seems like #236 maybe wasn't the problem, but forcing off the tests was the solution (as well as removing `find_package`).  
  
(I guess you guys might want to consider defaulting `BOOST_URL_BUILD_TESTS` to OFF).  
  
Thanks for you patience - I don't think building with cmake + Boost from vcpkg is exactly an obscure use case, so hopefully we can make things smoother for others.

---

## Comment 14

> Username: alandefreitas  
> Created at: 2022-07-28 08:07:13 UTC  
> Url: https://github.com/boostorg/url/issues/244#issuecomment-1197807656  

Well... the test subdir is only included when the option is ON:  
  
https://github.com/CPPAlliance/url/blob/5c307a889af674bcacb8ba4e5f15e1c9a949b6f9/CMakeLists.txt#L134-L136  
  
I'm updating the PR to link "boost_url_limits" properly to `Boost::headers` but your script shouldn't be creating "boost_url_limits" at all.  
  
You could try the complete `CPMAddPackage` overload, but I'm not sure it's going to make any difference. It might be handling the options differently.  
  
But if it works when you change the flag manually, then it just means you haven't cleared the cache before trying. Whenever you change an option in CMake you have to clear the cache.  
  
https://stackoverflow.com/questions/9680420/looking-for-a-cmake-clean-command-to-clear-up-cmake-output  
  
> the main repo and that also works.  
  
Yes. It should work because `BOOST_URL_BUILD_TESTS` is in the cache now. CMake can get this value from the command line, from the cache, or from the script. The first time you ran the script, it was stored in the cache. Whenever you clear the cache, it's going to stop working unless you provide `BOOST_URL_BUILD_TESTS` in the command line or in the script.  
  
> So it seems like https://github.com/CPPAlliance/url/pull/236 maybe wasn't the problem  
  
Yes. The description in #236 is not very good. This PR eventually became a number of sometimes unrelated small fixes to the cmake scripts, including some problems we brought up in this issue. Without #236 your script probably wouldn't work, because it wouldn't call find package.   
  
Since you are now setting options anyway, things might work for you without the `cmake_root` branch if you set the options `BOOST_URL_BUILD_TESTS=OFF` and `BOOST_URL_FIND_PACKAGE_BOOST=ON`. #236 is providing better defaults depending on the parent context.  
  
> consider defaulting BOOST_URL_BUILD_TESTS to OFF  
  
The default is not `ON`. The default `${BUILD_TESTING}`  
  
https://github.com/CPPAlliance/url/blob/5c307a889af674bcacb8ba4e5f15e1c9a949b6f9/CMakeLists.txt#L31  
  
`${BUILD_TESTING}` is a convention for users to determine if they want to run tests.   
  
https://cmake.org/cmake/help/latest/module/CTest.html  
  
In other words, `BUILD_TESTING` control tests in general and `BOOST_URL_BUILD_TESTS` control boost.url tests. That's why the default is neither `ON` nor `OFF`.  
  
> cmake + Boost from vcpkg is exactly an obscure use case,   
  
Yes. It's not. If vcpkg supports `find_package(Boost)` then support is the same. #236 should help a little, but both should be possible.  
  
> so hopefully we can make things smoother for others  
  
The boost review starts in two weeks, so it will be necessarily as smooth as any other boost library after that :crossed_fingers: .

---

## Comment 15

> Username: mark-99  
> Created at: 2022-07-28 10:13:11 UTC  
> Url: https://github.com/boostorg/url/issues/244#issuecomment-1197944835  

Ah the clearing of the cache. And people say cmake is unintuitive, they must be crazy. Ok.  
  
Yes getting it into vcpkg and Conan (either as an official part of Boost or before that) would probably help adoption.  
  
Anyway it's working now, I'll get to the actual using the library part now and see how that goes.

---

## Comment 16

> Username: sketch34  
> Created at: 2022-10-17 10:53:03 UTC  
> Updated at: 2022-10-17 11:02:30 UTC  
> Url: https://github.com/boostorg/url/issues/244#issuecomment-1280670201  

Is there documentation somewhere for how to build boost-url as a submodule with cmake and existing boost via vcpkg cmake toolchain file? Can't get it working and haven't got the time to try too hard right now! 🙏🏼  I'm on latest `develop` (6fb56ede700df0d74b616383e30f180aeaaf65e9). Latest `master` is the same.  
  
With `#include <boost/url/src.hpp>`  
  
Main `CMakeLists.txt`:  
```  
find_package(Boost 1.78.0 REQUIRED COMPONENTS timer)  
...  
  
set(BOOST_URL_BUILD_TESTS OFF)  
add_subdirectory(submodules/boost-url)  
```  
  
CMake generation is fine, but build has errors:  
  
```  
[build] FAILED: submodules/boost-url/CMakeFiles/boost_url.dir/src/src.cpp.o   
[build] /usr/local/bin/cmake -E time /opt/gcc/gcc-11.3/bin/g++ -DBOOST_URL_NO_LIB=1 -DBOOST_URL_STATIC_LINK=1 -I/home/jarrod/Dev/table-ws-ng/submodules/boost-url/include -isystem /home/jarrod/Dev/table-ws-ng/out/build/linux-debug/vcpkg_installed/x64-linux/include -g -std=gnu++20 -MD -MT submodules/boost-url/CMakeFiles/boost_url.dir/src/src.cpp.o -MF submodules/boost-url/CMakeFiles/boost_url.dir/src/src.cpp.o.d -o submodules/boost-url/CMakeFiles/boost_url.dir/src/src.cpp.o -c /home/jarrod/Dev/table-ws-ng/submodules/boost-url/src/src.cpp  
[build] In file included from /home/jarrod/Dev/table-ws-ng/submodules/boost-url/include/boost/url/grammar.hpp:32,  
[build]                  from /home/jarrod/Dev/table-ws-ng/submodules/boost-url/include/boost/url.hpp:13,  
[build]                  from /home/jarrod/Dev/table-ws-ng/submodules/boost-url/include/boost/url/src.hpp:28,  
[build]                  from /home/jarrod/Dev/table-ws-ng/submodules/boost-url/src/src.cpp:10:  
[build] /home/jarrod/Dev/table-ws-ng/submodules/boost-url/include/boost/url/grammar/string_view_base.hpp: In function ‘std::size_t boost::urls::grammar::hash_value(const boost::urls::grammar::string_view_base&)’:  
[build] /home/jarrod/Dev/table-ws-ng/submodules/boost-url/include/boost/url/grammar/string_view_base.hpp:838:16: error: ‘hash_value’ was not declared in this scope  
[build]   838 |         return hash_value(s.s_);  
[build]       |                ^~~~~~~~~~  
```

---

## Comment 17

> Username: mark-99  
> Created at: 2022-10-17 12:26:50 UTC  
> Updated at: 2022-10-17 12:33:41 UTC  
> Url: https://github.com/boostorg/url/issues/244#issuecomment-1280778562  

I got it working with vcpkg with a custom port (not a submodule):  
Set a custom port dir in CMakePresets.json: `"VCPKG_OVERLAY_PORTS": "${sourceDir}/ports",` (see the vcpkg docs).  
  
In ports/boost-url  
vcpkg.json:  
```  
{  
    "name": "boost-url",  
    "version-semver": "0.0.1",  
    "dependencies": [  
        {  
            "name": "vcpkg-cmake",  
            "host": true  
        },  
        {  
            "name": "vcpkg-cmake-config",  
            "host": true  
        }  
    ]  
}  
```  
portfile.cmake  
```  
vcpkg_from_github(  
    OUT_SOURCE_PATH SOURCE_PATH  
    REPO CPPAlliance/url  
    REF 67b444405736c1157d9c7a23bbd7470e3026a538  
    SHA512 22a7b024bdb9a0375341ee7e758bf6c9ced133367cf1642068711a10ae774646e509aba62998d65fc0a063e3a87c9b4a89e36f2635a7efda34cdeac12331fb50  
    HEAD_REF master  
)  
  
vcpkg_cmake_configure(  
    SOURCE_PATH "${SOURCE_PATH}"  
    OPTIONS -DBOOST_URL_BUILD_TESTS=OFF -DBUILD_TESTING=OFF -DBOOST_URL_BUILD_EXAMPLES=OFF  
)  
  
vcpkg_cmake_install()  
```  
`REF` is a git sha from the boost-url repo, or you can use tags etc., see the vcpkg docs.  
SHA512 is a file checksum, just set it to 0 and let it configure and it'll tell you what it expected, then fill that in.  
  
Then you can pull it in as if it was a Boost module:  
```  
find_package(Boost COMPONENTS coroutine filesystem iostreams thread url REQUIRED)  
target_link_libraries(${TARGET} PRIVATE Boost::coroutine Boost::filesystem Boost::iostreams Boost::thread Boost::url)  
```

---

## Comment 18

> Username: alandefreitas  
> Created at: 2022-10-17 19:13:46 UTC  
> Url: https://github.com/boostorg/url/issues/244#issuecomment-1281350389  

> build has errors  
  
It looks like you have to update boost.

---

## Comment 19

> Username: sketch34  
> Created at: 2022-10-18 11:20:59 UTC  
> Url: https://github.com/boostorg/url/issues/244#issuecomment-1282226973  

I updated my vcpkg boost dependencies to 1.80 and it worked with what I had above:  
  
```  
find_package(Boost 1.80.0 REQUIRED COMPONENTS timer)  
...  
  
set(BOOST_URL_BUILD_TESTS OFF)  
add_subdirectory(submodules/boost-url)  
```  
  
Side note: for those using vcpkg manifest mode, [this blog post](https://devblogs.microsoft.com/cppblog/take-control-of-your-vcpkg-dependencies-with-versioning-support/) is a useful read.
