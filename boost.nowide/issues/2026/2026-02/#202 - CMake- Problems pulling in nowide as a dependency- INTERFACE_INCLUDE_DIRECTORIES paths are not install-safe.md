# #202 - CMake: Problems pulling in nowide as a dependency: INTERFACE_INCLUDE_DIRECTORIES paths are not install-safe [Closed]

> Username: aral-matrix  
> Created at: 2026-02-21 12:35:31 UTC  
> Updated at: 2026-02-22 11:27:53 UTC  
> Closed at: 2026-02-22 10:22:19 UTC  
> Url: https://github.com/boostorg/nowide/issues/202  

Disclaimer: I apologize in advance, if the terminology I use is inaccurate - CMake is a real mess to learn from scratch.  
  
I have a minimal example of my problem here:  
https://codeberg.org/lars_uffmann/cmake-problem  
  
The initial problem is demonstrated with the default settings, where trying to configure cmake results in this error message:  
```  
CMake Error in build/_deps/boost_fetch-src/CMakeLists.txt:  
  Target "boost_nowide" INTERFACE_INCLUDE_DIRECTORIES property contains path:  
  
    "/home/user/cmake-bug/build/_deps/boost_fetch-src/include"  
  
  which is prefixed in the build directory.  
  
  
CMake Error in build/_deps/boost_fetch-src/CMakeLists.txt:  
  Target "boost_nowide" INTERFACE_INCLUDE_DIRECTORIES property contains path:  
  
    "/home/user/cmake-bug/build/_deps/boost_fetch-src/include"  
  
  which is prefixed in the build directory.Target "boost_nowide"  
  INTERFACE_INCLUDE_DIRECTORIES property contains path:  
  
    "/home/user/cmake-bug/build/_deps/boost_fetch-src/include"  
  
  which is prefixed in the source directory.  
```  
  
The instruction that triggers the error (and without which the project configures and builds fine) is the attempt to install FILE mylibTargets.cmake in line 281. Without boost nowide in the loop, this works fine.  
  
I hate to admit I tried using the LLM slop machine (mostly to confirm my belief that it produces rubbish, which it kinda did) to make some progress, and due to the - to put it mildly - beginner-unfriendly CMake documentation, I am unable to confirm what it said:  
  
Allegedly, the boost_nowide library (CMake absolutely refuses to work with the Boost::nowide alias) provided by your project contains include directories from the build interface, which are not safe during the install stage.  
  
So the slop machine suggested the workaround enabled by setting MYLIB_WORK_AROUND_ERROR to ON. Which changes the error to  
```  
CMake Error in CMakeLists.txt:  
  export called with target "boost_nowide_installed" which requires target  
  "boost_nowide" that is not in any export set.  
```  
  
From there on I am going to spare you the detour that the slop machine took me on (it just forwarded me from one useless suggestion to the next).  
  
I do not know what I am doing wrong, but I can't imagine it is *that* hard to integrate boost_nowide into my project (mylib) and I hope I am missing something obvious which you are able to point me to.  
  
Any help is appreciated!

---

## Comment 1

> Username: pdimov  
> Created at: 2026-02-21 13:27:49 UTC  
> Url: https://github.com/boostorg/nowide/issues/202#issuecomment-3938778806  

It's not a good practice to install dependencies acquired via FetchContent. Boost libraries, in particular, are definitely not intended to be installed piecemeal; they are intended to be installed as part of the entire Boost package.  
  
If you don't need to install Boost.Nowide as part of your library installation, you should set the CMake variable BOOST_SKIP_INSTALL_RULES to ON.

---

## Comment 2

> Username: Flamefire  
> Created at: 2026-02-21 13:32:01 UTC  
> Updated at: 2026-02-21 13:35:36 UTC  
> Url: https://github.com/boostorg/nowide/issues/202#issuecomment-3938785358  

I'm not fully sure what exactly you want to achieve. This library is part of Boost and requires other parts of Boost. So only downloading the single one won't work.  
  
There is a standalone version that is still being maintained by autogenerating it. The archive can be downloaded from the releases page, e.g. https://github.com/boostorg/nowide/releases/tag/v11.3.1  
Or the "standalone" branch that is updated with every commit to develop  
  
You can also set `Boost_NOWIDE_INSTALL/NOWIDE_INSTALL` to let the library handle the installation for you.  
But you are better off using a full Boost installation in the "regular" way and letting that handle the installation. You can use `BOOST_INCLUDE_LIBRARIES=nowide` (cmake) to only build the single library.  
  
If you use static libraries you don't need to install anything when building only an executable. However your approach `set(${ARG_LIB_NAME}_BUILD_SHARED_LIBS OFF CACHE BOOL "" FORCE)` won't work as only the standard `BUILD_SHARED_LIBS` is honored  
  
That being said: As Peter said above: You should rather use the Boost package, potentially with only letting it build/install a subset via the existing options

---

## Comment 3

> Username: aral-matrix  
> Created at: 2026-02-21 20:10:20 UTC  
> Updated at: 2026-02-21 20:13:42 UTC  
> Url: https://github.com/boostorg/nowide/issues/202#issuecomment-3939363343  

> It's not a good practice to install dependencies acquired via FetchContent  
  
@pdimov : this ties into what I want to achieve, so I'll give my reason here below in my answer to @Flamefire   
  
> I'm not fully sure what exactly you want to achieve. This library is part of Boost and requires other parts of Boost. So only downloading the single one won't work.  
  
As to what I want to achieve:  
- as a maintainer to the OpenXLSX library, I am reconfiguring the build environment to prepare packaging for debian  
- while doing so, I want to retain full compatibility with the windows users of our library  
- I want to give the user all of the following options for the dependencies:  
1. use system libraries, error out if a dependency is missing (on debian this would be handled by package dependencies)  
2. use system libraries, fetch missing libraries from source (repositories)  
3. fetch everything from source, ignore system libraries  
  
- additionally, I want to remain flexible in terms of:  
1. build a static library & the pkgconfig file to manage dependencies  
2. build a shared library & the pkgconfig file  
3. (not sure about this one yet) build a monolithic static library -> this requires potentially fetching a library from the source repository when the locally installed version exists but only as a shared object  
  
In terms of finding a package, I have not found a better solution than that provided to me by NLATP's `manage_dependency` function, which (@pdimov) is why the FetchContent is in there.  
CMake seems to be horrible at finding packages with the aforementioned flexibility. So I am thankful I have at least one working(?) solution. If you know of a better one that is as versatile, I am happy to learn.  
  
I was not aware (and it comes as a bit of a surprise) that nowide is not *meant* to be generated standalone. I seem to have misunderstood the whole separate repository for nowide - I thought that was exactly the purpose. I also did not experience a problem pulling it from your repo (previously using CPM.cmake) and building it as a subproject.  
  
If possible, I would very much prefer to not force users of OpenXLSX to download the *whole* boost repository to then only build a tiny fraction of it which also - correct me if I got that wrong - even functions as a header-only library.  
  
> There is a standalone version that is still being maintained by autogenerating it. The archive can be downloaded from the releases page, e.g. https://github.com/boostorg/nowide/releases/tag/v11.3.1 Or the "standalone" branch that is updated with every commit to develop  
  
Do I understand cmake correctly that I can *not* pull the standalone alone version via find_package / FetchContent, because it has a non-standard naming? I would like to avoid custom scripting that can break on updates.  
  
> However your approach `set(${ARG_LIB_NAME}_BUILD_SHARED_LIBS OFF CACHE BOOL "" FORCE)` won't work as only the standard `BUILD_SHARED_LIBS` is honored  
  
I think I am able to fix this part when polishing the CMake configuration.  
  
> That being said: As Peter said above: You should rather use the Boost package, potentially with only letting it build/install a subset via the existing options  
  
I would really like to avoid that - if possible. I just had a look and the full boost release is 50 Megabytes vs. a very modest 65 Kilobytes of nowide.  
  
I am quite opposed to bloating, and downloading all of boost to install a super lightweight library of it gives me a major bellyache.  
  
With the explanations you gave, am I understanding correctly that the nowide CMake configuration is intentionally designed to not work standalone? Is that another limitation of CMake I wasn't aware of, that there's no portable / generic way to integrate nowide nicely into boost while also functioning standalone?  
  
Edit: Thank you for your time! Sorry, I was so focused on my problem at hand that I forgot the etiquette.

---

## Comment 4

> Username: pdimov  
> Created at: 2026-02-21 20:27:52 UTC  
> Url: https://github.com/boostorg/nowide/issues/202#issuecomment-3939387649  

Installing some Boost library (under Debian) as part of your installation is going to create problems for users, because Debian already has a Boost package. If users install your library into the same directory where the system Boost resides (`/usr/include`), your installation of the Boost library will overwrite the system one and nothing good will result.  
  
And if they install it somewhere else, you'll now have two incompatible copies of that library (and its dependencies if any) which also tends to create problems because it's then easy to end up with the system Boost and the non-system Boost in the same program because of e.g. incorrectly set include paths.  
  
So that's generally not a good idea.  
  
But in this specific case you can use the standalone Nowide, from here, https://github.com/boostorg/nowide/releases/tag/v11.3.1  
  
This shouldn't intefere with the system copy of Boost.  
  
Or, you could avoid all that hassle and just require that Boost be available (that's your "(1) use system libraries, error out if a dependency is missing.") Under Debian, users will `apt-get install libboost-dev`, and under Windows, they can use `vcpkg install boost-nowide`.

---

## Comment 5

> Username: aral-matrix  
> Created at: 2026-02-21 21:56:17 UTC  
> Url: https://github.com/boostorg/nowide/issues/202#issuecomment-3939570043  

Thanks for the suggestions. I think I phrased that poorly - I have to date not ever packaged anything for any distro, hence me getting easily confused.  
My intention would not be to install nowide, but I think I got things a bit mixed up. The idea was that nowide is available to people using the OpenXLSX library, but I think that is really not a good idea - linkage of nowide should be internal to OpenXLSX, and not leak any files into the OS environment if OpenXLSX is installed, I agree with you there.  
  
If a user wants to use nowide themselves, they should install it on the system using their package manager and include it from the system location, regardless of whether they built OpenXLSX via pulling in the dependencies from github.  
  
Do you have a suggestion how to pull in the standalone nowide with FetchContent? Using the GIT_TAG 11.3.1 I think I would just get the regular version?

---

## Comment 6

> Username: pdimov  
> Created at: 2026-02-22 00:25:23 UTC  
> Url: https://github.com/boostorg/nowide/issues/202#issuecomment-3939775631  

I think that you can just use https://github.com/boostorg/nowide/releases/download/v11.3.1/nowide_standalone_v11.3.1.tar.gz directly. That would also be faster.

---

## Comment 7

> Username: aral-matrix  
> Created at: 2026-02-22 00:34:13 UTC  
> Url: https://github.com/boostorg/nowide/issues/202#issuecomment-3939786981  

You might be overestimating my proficiency with cmake. I would love to teach myself, but I tried - the documentation is so bad throughout that I can't make sense of it. So I learn by doing - I modify existing, working CMake configurations.  
  
Accordingly: I do not know how to "use xy.tgz directly" - as stated, I want to configure CMake so that it pulls in that archive as the dependency, ideally in a way that I keep it flexible to be able to up the version required in the future.  
  
Should I read your answer as "the standalone version can not be pulled in via cmake"?

---

## Comment 8

> Username: pdimov  
> Created at: 2026-02-22 00:58:52 UTC  
> Url: https://github.com/boostorg/nowide/issues/202#issuecomment-3939818085  

The [documentation of FetchContent](https://cmake.org/cmake/help/latest/module/FetchContent.html#overview) has these examples:  
```  
FetchContent_Declare(  
  googletest  
  GIT_REPOSITORY https://github.com/google/googletest.git  
  GIT_TAG        703bd9caab50b139428cea1aaff9974ebee5742e # release-1.10.0  
)  
  
FetchContent_Declare(  
  myCompanyIcons  
  URL      https://intranet.mycompany.com/assets/iconset_1.12.tar.gz  
  URL_HASH MD5=5588a7b18261c20068beabfb4f530b87  
)  
```  
The first one acquires the dependency via Git (as you do at the moment in your project) and the second one acquires it by downloading an archive.  
  
So, what you need to do in order to use the Nowide archive is this  
```  
FetchContent_Declare(  
  Nowide  
  URL     https://github.com/boostorg/nowide/releases/download/v11.3.1/nowide_standalone_v11.3.1.tar.gz  
  URL_HASH SHA256=eaec4d331e3961f5eeb10c46a11691d62047900a7a40765b0f23cdd3181e6ca6  
)  
```

---

## Comment 9

> Username: aral-matrix  
> Created at: 2026-02-22 02:35:28 UTC  
> Url: https://github.com/boostorg/nowide/issues/202#issuecomment-3939971610  

I implemented your suggestion in a new commit: https://codeberg.org/lars_uffmann/cmake-problem  
  
Now I am back to the exact same error - the archive from the URL is also not "install safe" and I believe what I actually need is a way to tell CMake to ignore the install interface of node? Sadly the `MYLIB_WORK_AROUND_ERROR = ON `is not it :(  
  
I would think I have complained about CMake enough, then again I always feel like I haven't yet... :/

---

## Comment 10

> Username: Flamefire  
> Created at: 2026-02-22 09:41:59 UTC  
> Url: https://github.com/boostorg/nowide/issues/202#issuecomment-3940593050  

> I was not aware (and it comes as a bit of a surprise) that nowide is not _meant_ to be generated standalone. I seem to have misunderstood the whole separate repository for nowide - I thought that was exactly the purpose. I also did not experience a problem pulling it from your repo (previously using CPM.cmake) and building it as a subproject.  
  
Boost is a collection of libraries designed to work together. They use each other to avoid implementing the same things over and over again.  
Hence yes, no Boost library is meant to be taken in isolation.  
The split into multiple repositories is for better organisation: Each has a different maintainer and it also doesn't require building all of Boost for a change in a single library.  
  
> If possible, I would very much prefer to not force users of OpenXLSX to download the _whole_ boost repository to then only build a tiny fraction of it which also - correct me if I got that wrong - even functions as a header-only library.  
  
You don't need the whole Boost repo: You can download the [super project ](https://github.com/boostorg/boost) and only the required libraries, i.e. the one(s) you want and their dependencies (submodules). Nowide has only Boost.Config as the dependency, besides required tools such as the global [CMake files](https://github.com/boostorg/cmake)  
That is result of modularisation efforts: You can actually build only parts of Boost. With `b2` this has always been possible: `b2 --with-xxx`, CMake has `BOOST_INCLUDE_LIBRARIES` to a similar effect and then tries hard to only look for what it really needs.  
  
And no, Nowide is not a header only library. Otherwise you wouldn't even need to "install" it  
  
> Do I understand cmake correctly that I can _not_ pull the standalone alone version via find_package / FetchContent, because it has a non-standard naming? I would like to avoid custom scripting that can break on updates.  
  
The standalone version is standalone, i.e. doesn't require any other Boost library and isn't "Boost". This is reflected in e.g. the CMake target being named `nowide::nowide` and other names containing "Boost" renamed.  
Looks like the tags should have been at the standalone branch, not develop to make consumption easier, but as Peter suggested you can download the standalone archives.  
  
> I am quite opposed to bloating, and downloading all of boost to install a super lightweight library of it gives me a major bellyache.  
  
See above: Use the system/user-provided one via find_package or download only the parts you need.  
  
> With the explanations you gave, am I understanding correctly that the nowide CMake configuration is intentionally designed to not work standalone? Is that another limitation of CMake I wasn't aware of, that there's no portable / generic way to integrate nowide nicely into boost while also functioning standalone?  
  
No it is not "intentionally" designed to *not work*. It is designed as part of Boost because it only works as part of Boost as it depends on e.g. Boost.Config.  
So it is simply *not* designed to work standalone, as that isn't an intended use case.  
  
> Now I am back to the exact same error - the archive from the URL is also not "install safe" and I believe what I actually need is a way to tell CMake to ignore the install interface of node?   
  
You are trying to install "another" library manually. If the library is meant to be installed it likely has means to be installed. The standalone version has the CMake variable `NOWIDE_INSTALL` for this.  
But as Peter said: While installing your library and another library you might accidentally overwrite system-wide installed libraries.  
However the standalone version can be installed alongside the Boost version, so at least you won't conflict with a `libboost*` package.  
  
And as you said your intention is a Debian package: Why bother at all and not use `find_package(Boost COMPONENTS nowide REQUIRED)`?

---

## Comment 11

> Username: aral-matrix  
> Created at: 2026-02-22 10:22:19 UTC  
> Url: https://github.com/boostorg/nowide/issues/202#issuecomment-3940655524  

> You don't need the whole Boost repo: You can download the [super project ](https://github.com/boostorg/boost)and only the required libraries, i.e. the one(s) you want and their dependencies (submodules). Nowide has only Boost.Config as the dependency, besides required tools such as the global [CMake files](https://github.com/boostorg/cmake)  
  
Okay that sounds like a feasible approach. I will try this then, and see how far I get. Thank you for that clarification.  
  
> And as you said your intention is a Debian package: Why bother at all and not use find_package(Boost COMPONENTS nowide REQUIRED)?  
  
Because debian is only my first attempt at packaging the library for any distro, while so far everyone uses the library directly from the repository - and find_package - to my understanding - is unable to pull in from a repository.  
  
Anyways, with your clarification above, it seems the boost super project is the way to go and I assume for "COMPONENTS" I can choose nowide / Boost::nowide then. I will spend some time on this, and I suppose I will open a new issue if I have nowide-specific questions afterwards. I don't want to take up any more of your time.  
  
Thank you for the suggestions!

---

## Comment 12

> Username: aral-matrix  
> Created at: 2026-02-22 10:31:55 UTC  
> Url: https://github.com/boostorg/nowide/issues/202#issuecomment-3940670988  

Okay maybe one last question:  
  
What is the command to FetchContent to retrieve nowide & relevant stuff only from the super repository? I tried "COMPONENTS Nowide" (and nowide and boost_nowide and Boost::nowide) and neither work. So I believe the "COMPONENTS" argument may not be the way to do this, as the FetchContent documentation also does not mention the variable.

---

## Comment 13

> Username: aral-matrix  
> Created at: 2026-02-22 10:49:28 UTC  
> Url: https://github.com/boostorg/nowide/issues/202#issuecomment-3940694386  

```  
include(FetchContent)  
  
# ---- Boost.Config ----  
FetchContent_Declare(  
  boost_config  
  GIT_REPOSITORY https://github.com/boostorg/config.git  
  GIT_TAG        boost-1.86.0  
)  
  
# ---- Boost.Nowide ----  
FetchContent_Declare(  
  boost_nowide  
  GIT_REPOSITORY https://github.com/boostorg/nowide.git  
  GIT_TAG        boost-1.86.0  
)  
  
FetchContent_MakeAvailable(  
  boost_config  
  boost_nowide  
)  
```  
Is that how it is done?

---

## Comment 14

> Username: Flamefire  
> Created at: 2026-02-22 11:09:11 UTC  
> Url: https://github.com/boostorg/nowide/issues/202#issuecomment-3940718771  

`COMPONENTS` is for `find_package`  
You likely need `GIT_SUBMODULES` and pass the super-project to `Fetch_content`, not individual repos or you'd need to place them somehow in the right folders

---

## Comment 15

> Username: aral-matrix  
> Created at: 2026-02-22 11:27:53 UTC  
> Url: https://github.com/boostorg/nowide/issues/202#issuecomment-3940741689  

Any chance you have a working call to FetchContent_Declare that uses GIT_SUBMODULES correctly? I am again failing to find a submodule name that is recognize / does not produce an error.
