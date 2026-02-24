# #77 - Document BoostConfig.cmake? [Open]

> Username: SamB  
> Created at: 2024-11-25 21:02:35 UTC  
> Updated at: 2026-01-30 18:14:56 UTC  
> Url: https://github.com/boostorg/cmake/issues/77  

The documentation for your version of BoostConfig.cmake seems a little sparse: https://github.com/boostorg/cmake/blob/7f5336b3bf8067bb40da4e8b9940c133271e938a/config/BoostConfig.cmake#L5  
  
CMake 3.30 deprecated [FindBoost](https://cmake.org/cmake/help/latest/module/FindBoost.html), saying:  
  
> Changed in version 3.30: This module is available only if policy [`CMP0167`](https://cmake.org/cmake/help/latest/policy/CMP0167.html#policy:CMP0167) is not set to NEW. Port projects to upstream Boost's `BoostConfig.cmake` package configuration file, for which `find_package(Boost)` now searches.  
  
Sounds reasonable, except they didn't mention that there are two of those, <https://github.com/boostorg/boost_install/blob/develop/BoostConfig.cmake> and <https://github.com/boostorg/cmake/blob/develop/config/BoostConfig.cmake>,  neither of which appears to be fully documented in its own right, seemingly depending on the FindBoost documentation to describe how things are meant to work.  
  
Fortunately, the CMake folks don't seem the type to actually remove deprecated stuff in a hurry, especially not the documentation, but its not exactly clear what the user is actually supposed to before flipping this policy *especially* if they want to make sure their software works with both the "boost_install" and "cmake" variants.  
  
P.S. I sure wish I could open this against *both* repositories. Darn you, github!

---

## Comment 1

> Username: ulatekh  
> Created at: 2025-08-04 18:52:56 UTC  
> Updated at: 2025-08-04 19:51:08 UTC  
> Url: https://github.com/boostorg/cmake/issues/77#issuecomment-3151978757  

I find this lack of documentation baffling, too. Doesn't look like there's much help here on GitHub, either. My next step is to explore the mailing lists, but that looks pretty disorganized. So here's what I figured out on my own.  
  
* Set `Boost_DIR` to the directory that contains the *installed* BoostConfig.cmake. \  
In my installation, that looks something like `%BOOST_ROOT%\lib64-msvc-14.2\cmake\Boost-1.86.0` . \  
Fortunately, my CMake buildsystem's existing handling of Boost had enough information to synthesize this path. \  
Be sure to set `Boost_DIR` as a cached string variable, e.g. `set(Boost_DIR "${path}" CACHE STRING "Location of BoostConfig.cmake")`.  
* You have to set the value of `Boost_INCLUDE_DIR` yourself now. \  
`get_target_property(include_dirs Boost::headers INTERFACE_INCLUDE_DIRECTORIES)` will bafflingly give you the parent directory of your Boost installation. \  
Fortunately, my CMake buildsystem's existing handling of Boost had enough information to synthesize this path. \  
Be sure to set `Boost_INCLUDE_DIR` as a cached string variable, e.g. `set(Boost_INCLUDE_DIR "${path}" CACHE STRING "Location of Boost header files")`.  
* When running `find_package(Boost CONFIG ...)` for a project, i.e. to set up link libraries, you'll have to generate your own value for the old `Boost_LIBRARIES` variable. \  
This requires a change to the supplied `BoostConfig.cmake` file. \  
Attached is a patch for "git apply", that works even when Boost is not in git. \  
[boost-config-cmake.patch](https://github.com/user-attachments/files/21583414/boost-config-cmake.patch)  
It must be applied to all files found by lib*/cmake/Boost-*/BoostConfig.cmake when Git Bash is run from the BOOST_ROOT directory.  
  
These steps got my (relatively simple) Boost CMake scripts working again.  
  
If there's actual documentation for any of this, or a better way to do any of this, I'm all ears.

---

## Comment 2

> Username: pdimov  
> Created at: 2025-08-04 21:15:52 UTC  
> Url: https://github.com/boostorg/cmake/issues/77#issuecomment-3152429579  

You shouldn't be using the variables; they are obsolete. Link to the appropriate targets instead.

---

## Comment 3

> Username: ulatekh  
> Created at: 2025-08-04 21:33:19 UTC  
> Url: https://github.com/boostorg/cmake/issues/77#issuecomment-3152468514  

Then how are we supposed to use the new Boost CMake configuration script in a way that's backwards compatible with the old script? My CMake build-system supports a lot of different compilers/versions/platforms simultaneously.  
  
I'm also not thrilled with how the new script makes us do the same thing twice, i.e. list components in the parameters to `find_package(Boost CONFIG)` and then again when linking. A generated value for `Boost_LIBRARIES` was much more handy.

---

## Comment 4

> Username: pdimov  
> Created at: 2025-08-05 06:32:15 UTC  
> Url: https://github.com/boostorg/cmake/issues/77#issuecomment-3153680527  

Using targets is the recommended CMake practice (in general, not just for Boost) since CMake 3 ("modern CMake".) FindBoost defines the targets since CMake 3.5. In addition to giving you the include path automatically, targets can also give you compile definitions or compile requirements.  
  
While Boost_LIBRARIES may be slightly more convenient in simple cases, targets are more flexible because you can link to just the necessary targets if you have more than one executable (or library.) E.g.  
  
```  
find_package(Boost COMPONENTS lib1 lib2)  
  
add_executable(foo foo.cpp)  
target_link_libraries(foo PRIVATE Boost::headers Boost::lib1)  
  
add_executable(bar bar.cpp)  
target_link_libraries(bar PRIVATE Boost::headers Boost::lib2)  
```

---

## Comment 5

> Username: ulatekh  
> Created at: 2025-08-05 16:12:58 UTC  
> Url: https://github.com/boostorg/cmake/issues/77#issuecomment-3155770979  

Our CMake buildsystem allows each target to call `find_package(Boost)` with a different list of components, so I put it to you that your example is the simple case.  
  
It also hardcodes the idea that the target for lib1 is `Boost::lib1`, instead of allowing `BoostConfig.cmake` to determine its name.  
  
Finally, there's no replacement for `Boost_INCLUDE_DIR`; as stated above, `get_target_property(include_dirs Boost::headers INTERFACE_INCLUDE_DIRECTORIES)` will bafflingly give you the parent directory of your Boost installation. Is there another way to get the Boost include-directories root that I haven't spotted?

---

## Comment 6

> Username: pdimov  
> Created at: 2025-08-06 13:02:47 UTC  
> Url: https://github.com/boostorg/cmake/issues/77#issuecomment-3160082117  

This should work. Linking to Boost::headers needs to give you the right include directory, so the above approach should, too.  
  
https://github.com/boostorg/boost_install/blob/c9fbbd023650a85e80a649b1d5fb5c40c1fc15eb/BoostConfig.cmake#L235

---

## Comment 7

> Username: ulatekh  
> Created at: 2025-08-06 15:30:12 UTC  
> Updated at: 2025-08-06 17:10:20 UTC  
> Url: https://github.com/boostorg/cmake/issues/77#issuecomment-3160642295  

Linking to `Boost::headers` to get the include directory doesn't seem to work in any Visual Studio build. I still need to synthesize `Boost_INCLUDE_DIR` there. It's not necessary in the MinGW/gcc build, or the Linux build, since there, the Boost headers are stored in a standard location, e.g. `/mingw64/include` and `/usr/include` respectively. Also, is there any existing documentation on the meaning of the `Boost::headers` target?  
  
And what about the danger of exposing internal details, such as raw target names? My patch above, which sets the `Boost_LIBRARIES` variable with everything that needs to be linked to, based on the most recent call to `find_package(Boost CONFIG ...)`, would be one way to achieve that, plus it helps with backwards compatibility. What do you think?  
  
Finally, how is `Boost_DIR` intended to be set? I was unable to find any simple method; I had to synthesize the entire path based on information I coincidentally had laying around in my CMake buildsystem.  
  
Thank you for your help with this.

---

## Comment 8

> Username: pdimov  
> Created at: 2025-08-06 21:38:19 UTC  
> Url: https://github.com/boostorg/cmake/issues/77#issuecomment-3161708402  

BoostConfig does already set Boost_INCLUDE_DIRS and Boost_LIBRARIES for compatibility since Boost 1.71. The problem is that in your CML you can't rely on finding Boost 1.71 or later.  
  
This wasn't a problem before, because FindBoost set those variables even when using BoostConfig. But it is now.  
  
And we can't do anything about that because we can't fix old versions of Boost. :-)  
  
Boost_DIR is a CMake variable that needs to be set to the exact path of BoostConfig.cmake. We can't do anything about that, either. Setting Boost_ROOT is a bit more flexible, but still not as flexible as setting BOOST_ROOT used to be when using FindBoost.

---

## Comment 9

> Username: ulatekh  
> Created at: 2025-08-06 23:20:35 UTC  
> Url: https://github.com/boostorg/cmake/issues/77#issuecomment-3161898836  

If BoostConfig was setting `Boost_INCLUDE_DIR` and `Boost_LIBRARIES`, I wouldn't have had to embark upon this investigation in the first place. I am setting `BOOST_ROOT` in my environment variables. And I am presently using Boost 1.86.  
  
I don't remember where I got my instructions for building the Visual Studio version of Boost, but it looks like this:  
  
* Open a "native tools" command prompt for Visual Studio (in this example, VS2019)  
* cd to the `%BOOST_ROOT%` directory  
* `.\bootstrap.bat`  
* `b2 -j 8 toolset=msvc-14.2 threading=multi link=shared address-model=32 variant=debug`  
* `b2 -j 8 toolset=msvc-14.2 threading=multi link=shared address-model=32 variant=release`  
* `move stage\lib lib32-msvc-14.2`  
* `b2 -j 8 toolset=msvc-14.2 threading=multi link=shared address-model=64 variant=debug`  
* `b2 -j 8 toolset=msvc-14.2 threading=multi link=shared address-model=64 variant=release`  
* `move stage\lib lib64-msvc-14.2`  
  
Then, for a 64-bit VS2019 build, I have to set `Boost_DIR` to `%BOOST_ROOT%\lib64-msvc-14.2\cmake\Boost-1.86.0`.  
  
Is there a different way I should be building/installing Boost for Visual Studio? And don't say NuGet...that causes a lot of problems, not the least of which are installing a new copy of Boost for each solution, and the requirement to be connected to the Internet.

---

## Comment 10

> Username: pdimov  
> Created at: 2025-08-07 05:49:36 UTC  
> Url: https://github.com/boostorg/cmake/issues/77#issuecomment-3162589836  

As I said, BoostConfig has been setting the variables since 1.71:  
  
https://github.com/boostorg/boost_install/commit/cfa8d55250dfc2635e907e42da423e4eb540dee5  
https://github.com/boostorg/boost_install/commit/dcd92dc506470781df2be641bf88bb964c7b6d4c  
  
The variable is Boost_INCLUDE_DIRS though, not Boost_INCLUDE_DIR:  
  
https://cmake.org/cmake/help/latest/module/FindBoost.html#result-variables  
  
The installation procedure should generally be `b2 install` with the appropriate `--prefix` and all the variants at once, e.g. `address-model=32,64 variant=debug,release` or just `--built-type=complete`. If you move the directories after installation, the embedded paths end up wrong.

---

## Comment 11

> Username: ulatekh  
> Created at: 2025-08-13 17:22:44 UTC  
> Updated at: 2025-08-13 21:58:11 UTC  
> Url: https://github.com/boostorg/cmake/issues/77#issuecomment-3184805295  

I'm now using the `b2 install` method, and now `find_package(Boost CONFIG)` works as advertised! I `set(Boost_INCLUDE_DIR "${Boost_INCLUDE_DIRS}")` to maintain backward compatibility, and even though `Boost_LIBRARIES` is empty, the necessary libraries seem to end up in my projects' list of library dependencies. Thank you for all your help!  
  
Now that I know about `b2 install` for the Boost source distribution, I can search for the [instructions](https://www.boost.org/doc/libs/1_88_0/tools/build/doc/html/index.html), but I didn't know that beforehand.

---

## Comment 12

> Username: nigels-com  
> Created at: 2025-12-14 08:07:55 UTC  
> Url: https://github.com/boostorg/cmake/issues/77#issuecomment-3650456372  

@ulatekh Anything further needed for this?  
Seems like time to close this ticket.

---

## Comment 13

> Username: gast128  
> Created at: 2026-01-30 15:33:27 UTC  
> Updated at: 2026-01-30 15:35:07 UTC  
> Url: https://github.com/boostorg/cmake/issues/77#issuecomment-3824340308  

I am trying to get this working but it fails on Windows with VS2022 after numerous attempts. We have downloaded Boost 1.86 in separate folder ('D:\Develop\SDK and Libraries\boost\1_86_0') and build the stuff with b2. Now we want to link the Boost libraries with CMake. With the old find_package in module mode CMake finds it (with providing the hint of BOOST_ROOT) but with the new config method (introduced in CMake 3.30) it will not work. CMake documentation states that it just should work with BoostConfig.cmake provided by Boost.  
  
- without Boost_DIR hint it cannot find Boost: 'Could NOT find Boost (missing: Boost_DIR)'  
- With Boost_DIR  hint:   'Found package configuration file:  
  
    D:/Develop/SDK and Libraries/boost/1_86_0/tools/cmake/config/BoostConfig.cmake  
  
  but it set Boost_FOUND to FALSE so package "Boost" is considered to be NOT  
  FOUND.  Reason given by package:  
  
  A required dependency, boost_headers, has not been found.'  
- If the version is specified:   'Could not find a configuration file for package "Boost" that is compatible  
  with requested version "1.86".  
  
  The following configuration files were considered but not accepted:  
  
    D:/Develop/SDK and Libraries/boost/1_86_0/tools/cmake/config/BoostConfig.cmake, version: unknown'  
  
  
```  
cmake_minimum_required(VERSION 3.30)  
   
project(MyProgram  
    VERSION 1  
    LANGUAGES CXX  
)  
  
add_executable(${PROJECT_NAME} main.cpp)  
  
set(SDK_ROOT "D:/Develop/SDK and Libraries")  
  
cmake_policy(SET CMP0167 NEW)  
set(BOOST_ROOT "${SDK_ROOT}/boost/1_86_0")  
set(Boost_DIR "${SDK_ROOT}/boost/1_86_0/tools/cmake/config")  
      
#find_package(Boost 1.86.0 CONFIG COMPONENTS program_options)  
find_package(Boost CONFIG COMPONENTS program_options)  
      
target_link_libraries(${PROJECT_NAME}  
        PRIVATE   
        Boost::program_options  
    )  
  
```

---

## Comment 14

> Username: pdimov  
> Created at: 2026-01-30 15:50:08 UTC  
> Url: https://github.com/boostorg/cmake/issues/77#issuecomment-3824410857  

```  
set(Boost_DIR "${SDK_ROOT}/boost/1_86_0/tools/cmake/config")  
```  
This is not the correct directory for `Boost_DIR`.  
  
How have you built Boost with b2? If you use `b2 install` (recommended), you should point `Boost_DIR` to the appropriate directory within the Boost install directory.  
  
If you use `b2 stage` to build in-place (not recommended), then you should point Boost_DIR to `stage/lib/cmake/Boost-1.86.0`.

---

## Comment 15

> Username: gast128  
> Created at: 2026-01-30 16:58:04 UTC  
> Url: https://github.com/boostorg/cmake/issues/77#issuecomment-3824725446  

Thx for then answer. We build without install option. If I specify the following:  
  
`b2 install --prefix=.\stage toolset=msvc-14.3 variant=debug threading=multi link=shared address-model=64 define=_BIND_TO_CURRENT_VCLIBS_VERSION  
`  
and then   
  
`set(Boost_DIR "D:/Develop/SDK and Libraries/boost/1_86_0/stage") `  
  
it still doesn't work. There is also no BoostConfig.cmake in the stage directory.

---

## Comment 16

> Username: pdimov  
> Created at: 2026-01-30 17:47:41 UTC  
> Url: https://github.com/boostorg/cmake/issues/77#issuecomment-3824915469  

You shouldn't be installing into `./stage`. Either install into a directory that is completely separate from the source directory, or use `b2 stage` to build into `./stage`.  
  
When you use `b2 stage` (or just `b2`), BoostConfig.cmake should be in  
```  
$BOOST_ROOT\stage\lib\cmake\Boost-1.86.0  
```  
  
When you use `b2 install --prefix=...`, BoostConfig.cmake should be in  
```  
$PREFIX\lib\cmake\Boost-1.86.0  
```  
  
In the latter case, instead of setting Boost_DIR to the above exact directory, it should be possible to set Boost_ROOT to the prefix directory; CMake should then find the BoostConfig.cmake file by itself.

---

## Comment 17

> Username: gast128  
> Created at: 2026-01-30 18:14:55 UTC  
> Url: https://github.com/boostorg/cmake/issues/77#issuecomment-3825023269  

Yes this works. In retrospect the stage directory worked also. So indeed if PREFIX was 'D:\Temp' one needs in CMakeLists.txt:  
  
`set(BOOST_ROOT "D:/Temp")`  
  
or  
  
`set(Boost_DIR "D:/Temp/lib/cmake/Boost-1.86.0")`  
  
Thx. Perhaps somebody could document this in Boost documentation if not already there or in the CMake documentation.
