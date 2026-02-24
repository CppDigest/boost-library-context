# #66 - CMake support [Closed]

> Username: friendlyanon  
> Created at: 2020-12-31 18:57:21 UTC  
> Updated at: 2023-12-06 18:03:53 UTC  
> Closed at: 2021-05-31 01:27:59 UTC  
> Url: https://github.com/boostorg/pfr/issues/66  

At the moment this library does not support clients using CMake. Is there a plan to change that?  
  
The documentation's `You can just copy the content of the "include" folder` suggestion is not acceptable.  
  
Since this is a header-only library with no dependency on other Boost libraries, the lists file to provide support would look trivial.    
I could make a PR adding a lists file if desired.

---

## Comment 1

> Username: apolukhin  
> Created at: 2021-01-02 14:01:33 UTC  
> Url: https://github.com/boostorg/pfr/issues/66#issuecomment-753477331  

I'd appreciate a PR with CMake support. Please provide tests and usage examples.

---

## Comment 2

> Username: friendlyanon  
> Created at: 2021-01-02 14:33:21 UTC  
> Updated at: 2021-01-02 14:58:02 UTC  
> Url: https://github.com/boostorg/pfr/issues/66#issuecomment-753480790  

Sure, I already have a minimal implementation at [cmake-support](https://github.com/friendlyanon/pfr/tree/cmake-support), which only defines the usage requirements of the project, but it follows a convention for naming and directory structure that you might not like, so let me ask a few questions first:  
  
How would you like clients to find your library? At the moment the lists file defines `<prefix>/lib/cmake/BoostPFR-2.0.1` as the install location for the config file that describes the project and `<prefix>/include/BoostPFR-2.0.1` as the header root . This path is [trivially findable by CMake](https://github.com/friendlyanon/blobify/blob/master/CMakeLists.txt#L35), but I want to know what is your stance on the name `BoostPFR` and the version embedded in the folder name.  
  
What is the semver compatibility you want to provide? At the moment the support is declared as [`SameMajorVersion`](https://github.com/friendlyanon/pfr/blob/cmake-support/CMakeLists.txt#L48). CMake documentation on the functionality: https://cmake.org/cmake/help/latest/module/CMakePackageConfigHelpers.html#generating-a-package-version-file  
  
How would you like the exported target name to be called? At the moment the [library target is called `pfr`](https://github.com/friendlyanon/pfr/blob/cmake-support/CMakeLists.txt#L42) and when installing this is [namespaced to `Boost::`](https://github.com/friendlyanon/pfr/blob/cmake-support/CMakeLists.txt#L60), i.e. it becomes `Boost::pfr` in clients' lists files. There is also the [`ALIAS` target](https://github.com/friendlyanon/pfr/blob/cmake-support/CMakeLists.txt#L20) which makes the usage uniform even if the client decides to pull the library into their build tree:  
```cmake  
# Installed in a prefix location  
find_package(BoostPFR)  
# Or pulled into the build tree  
add_subdirectory("path to pfr")  
  
# In both cases the usage looks the same  
target_link_libraries(tgt PRIVATE Boost::pfr)  
```  
  
How should the testing be done? I see in the CI that b2 is used to drive the jobs, but I'm not familiar with that tool. I'm also generally not familiar with Boost, so without understanding how things work I cannot provide the lists file for running tests.

---

## Comment 3

> Username: apolukhin  
> Created at: 2021-01-03 14:47:42 UTC  
> Url: https://github.com/boostorg/pfr/issues/66#issuecomment-753628200  

Please follow the existing practice where possible. Here's an example of CMake for another header-only boost library https://github.com/boostorg/any/blob/develop/CMakeLists.txt .  
  
* `BoostPFR-2.0.1` seems good. SameMajorVersion is what I was intending in the library.  
* Lowercase target name seems better `Boost::pfr`. It would also follow the existing practice https://github.com/boostorg/mp11/blob/develop/CMakeLists.txt  
* Skip the tests rewrite on CMake. Leave it b2.  
* Provide some minimal usage example with CMake. So that I could embed that example in the b2 test suite and make sure that the CMake is working fine.

---

## Comment 4

> Username: friendlyanon  
> Created at: 2021-01-03 16:51:05 UTC  
> Url: https://github.com/boostorg/pfr/issues/66#issuecomment-753644913  

That lists file from `any` is woefully incomplete, I'm unsure what is there to follow. The target name should have a name so that prefixed with a namespace it ends up as the desired name. If the target is called `boost_pfr` then the resulting export set with a namespace prefix will install as `Boost::boost_pfr`, which is really undesirable.  
  
[cmake-support](https://github.com/friendlyanon/pfr/tree/cmake-support) at the moment contains a "complete" lists file, which can be trivially consumed and installed like so:  
```bash  
# assuming UNIX and the build system is Ninja or Make  
cmake -S . -B build -D CMAKE_BUILD_TYPE=Release  
cmake --build build  
sudo cmake --install build  
```  
The above commands with the current lists file will do the correct thing on all platforms, but also enable trivial packaging with `cpack`:  
```bash  
(cd build && cpack) # will use the default generators, but parameterizing is trivial  
```  
I am in fact [using this](https://github.com/friendlyanon/blobify/blob/master/cmake/install-dependencies.cmake#L72-L74) property of it being trivially consumable, because I don't want to package and maintain a personal Debian repository for this.

---

## Comment 5

> Username: apolukhin  
> Created at: 2021-01-03 17:20:25 UTC  
> Url: https://github.com/boostorg/pfr/issues/66#issuecomment-753648920  

Hm... Then please do it the right way :)  
  
Please, also write a letter to the ["Boost developers mailing list"](https://www.boost.org/community/groups.html) with the description of issues with the current cmake approach. People just started the cmake transition and some things may be done wrong. We'd appreciate a guidance or at least some hints on how to do it better

---

## Comment 6

> Username: friendlyanon  
> Created at: 2021-01-03 21:53:04 UTC  
> Url: https://github.com/boostorg/pfr/issues/66#issuecomment-753681459  

I have no idea how mailing lists work. However, I am all for the Boost project achieving better CMake usage.  
  
Suffice to say, CMake usage and dependency management in general is in a pretty bad situation when it comes to C++. The Boost project adopting better CMake usage and acquiring an exemplary status should potentially alleviate these common pains.

---

## Comment 7

> Username: friendlyanon  
> Created at: 2021-05-31 01:27:59 UTC  
> Url: https://github.com/boostorg/pfr/issues/66#issuecomment-851107952  

Peter is hard at work to bring CMake to Boost, so I can safely close this issue.

---

## Comment 8

> Username: SomeoneSerge  
> Created at: 2023-12-05 17:38:32 UTC  
> Url: https://github.com/boostorg/pfr/issues/66#issuecomment-1841298186  

Hi! I see the original post mentions `find_package` support, and currently the master branch doesn't contain neither the install rules nor `BoostPFRConfig.cmake`. As a consequence I see some parties consume `pfr` using `FetchContent` without `FIND_PACKAGE_ARGS`, e.g. https://github.com/Meumeu/WiVRn/blob/23298a1581e792c1ac7a09db238a87e7ad7c7c2c/CMakeLists.txt#L84 (which is obviously damaging to ecosystems like vcpkg or nixpkgs, etc)  
  
I'm not familiar with the CMake support efforts in Boost, so I thought I'd ask before opening follow-up issues or PRs. What are the current plans regarding the install rules and `find_package`? Thanks!

---

## Comment 9

> Username: friendlyanon  
> Created at: 2023-12-06 15:06:17 UTC  
> Url: https://github.com/boostorg/pfr/issues/66#issuecomment-1843073947  

Boost.PFR is a [Level 0](https://pdimov.github.io/boostdep-report/boost-1.83.0/module-levels.html) header-only library and Peter Dimov has introduced CMake client support to Boost. You can `find_package(Boost REQUIRED)` and link to `Boost::headers` for PFR.

---

## Comment 10

> Username: SomeoneSerge  
> Created at: 2023-12-06 18:03:38 UTC  
> Updated at: 2023-12-06 18:03:53 UTC  
> Url: https://github.com/boostorg/pfr/issues/66#issuecomment-1843404773  

Thanks! If I were to contact wivrn developers, would it be reasonable to suggest that they depend on the whole `Boost::headers` instead?
