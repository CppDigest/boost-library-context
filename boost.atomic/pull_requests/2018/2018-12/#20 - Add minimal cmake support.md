# #20 Add minimal cmake support [Closed]

> Username: Mike-Devel  
> Created at: 2018-12-16 12:11:05 UTC  
> Updated at: 2018-12-20 19:00:16 UTC  
> Closed at: 2018-12-18 16:32:35 UTC  
> Url: https://github.com/boostorg/atomic/pull/20  

Allows the integration of a local Boost.Atomic copy into a cmake project via `add_subdirectory( libs/atomic )`

---

## Comment 1

> Username: Lastique  
> Created_at: 2018-12-18 16:32:35 UTC  
> Url: https://github.com/boostorg/atomic/pull/20#issuecomment-448283031  

A more reduced CMakeLists.txt has been added in 5ad28ff.  
  
I intentionally don't want CMakeLists.txt to be used for building the library as the proposed CMakeLists.txt doesn't handle various build variants.

---

## Comment 2

> Username: Mike-Devel  
> Created_at: 2018-12-18 17:21:03 UTC  
> Updated_at: 2018-12-18 17:22:18 UTC  
> Url: https://github.com/boostorg/atomic/pull/20#issuecomment-448299899  

> I intentionally don't want CMakeLists.txt to be used for building the library  
  
Wouldn't that kind of defy the purpose of this file? Or can the library be used header-only?  
  
> as the proposed CMakeLists.txt doesn't handle various build variants.  
  
Do you mean variants as in static/dynamic, multi/single-threaded, static/dynamic crt, cxx 03/11/14/17.... or different variants of Boost.Atomic specifically ( v1 / v2 / with_featureXX)?   
  
Correct me if I'm wrong, but I don't believe any of the latter exist (but I could amend the PR accordingly). The former however is supported and in fact, the whole purpose of my PRs is that you can easily build all libraries with the same configuration as the "main" cmake project that is using them without specifying all those parameters consistently in cmake as well as boost build, by just calling `add_subdirectory(libs/<libname>)` in the parent cmake file (just as I did in `test/test_cmake/CMakeLists.txt`) .   
  
But just to be clear: Contrary to what Robert had in mind, the purpose of my PRs is not to provide a solution that can be used instead of boost build and produce the same artifacts in the same directories. Their purpose is to provide functionality orthogonal to what the current build system does.

---

## Comment 3

> Username: Mike-Devel  
> Created_at: 2018-12-18 17:23:40 UTC  
> Url: https://github.com/boostorg/atomic/pull/20#issuecomment-448300755  

I guess the library you copied the CMakeLists.txt file in https://github.com/boostorg/atomic/commit/5ad28ffd18559be0ffba40e7400d865a96320957 from is a header-only library?

---

## Comment 4

> Username: Lastique  
> Created_at: 2018-12-18 18:07:24 UTC  
> Url: https://github.com/boostorg/atomic/pull/20#issuecomment-448314874  

> Wouldn't that kind of defy the purpose of this file?  
  
I'm not totally sure about the purpose, to be honest. It looks like you want to have include directories automatically added in your CMake project, for all Boost dependencies, recursively. This CMakeLists.txt does this.  
  
> Or can the library be used header-only?  
  
It can, as long as you don't instantiate `atomic<>` on a type that is not natively supported.  
  
> Do you mean variants as in static/dynamic, multi/single-threaded, static/dynamic crt, cxx 03/11/14/17....  
  
Yes, all that stuff plus RTL variants on Windows and what not.  
  
> The former is supported and in fact, the whole purpose of my PRs is that you can easily build all libraries with the same configuration as the "main" cmake project that is using them without specifying all those parameters consistently in cmake as well as boost build, by just calling add_subdirectory(libs/<libname>) in the parent cmake file.  
  
But your PR doesn't do any name mangling. This breaks autolinking, for example. Also, AFAICT, it doesn't handle build dependency feature propagation or any kind of conditional dependencies, like Boost.Build does. Like, for example, linking with `kernel32` when building with gcc on Windows or defining macros while building the library. See the Jamfile. Some more advanced trickery is used in larger libraries like Boost.Log, and until there is a reasonable way to support building *any* Boost library, the way Boost.Build does, I don't want to add half solutions.  
  
> Their purpose is to provide functionality orthogonal to what the current build system does.  
  
As I said above, they don't provide enough.  
  
So, for now, you have to build Boost separately, with Boost.Build. You can use CMakeLists.txt in your project to add include directories. You'll have a problem with library directory (which will be just one) and maybe libraries (not sure how that would work in CMake).

---

## Comment 5

> Username: Mike-Devel  
> Created_at: 2018-12-18 19:45:44 UTC  
> Url: https://github.com/boostorg/atomic/pull/20#issuecomment-448345886  

> I'm not totally sure about the purpose, to be honest. It looks like you want to have include directories automatically added in your CMake project  
  
I'm sorry then that I haven't made myself clear enough, but adding include directories is really the least of my worries. If I have to run b2 anyway to get the compiled libraries, all boost headers will be reachable from a single directory anyway an I can easily add that to my CMake script.  
  
What I want to achieve is exactly what I'm doing in the test: Add all libraries (boost or otherwise) that my project depends on into e.g. `<project-root>/libs/<lib-name>`,  write an `add_subdirectory( libs/<libs-name)` into my projects `CMakeList.txt file` together with the rule to build my app:  
  
    add_executable(my_app file1.cpp file2.cpp)  
    target_link_libraries(my_app <lib1> <lib2> <lib3>  
  
An then compile everything together in one go to make sure that all libraries are built with the same compiler, the same c++ version, the same settings for static/dynamic runtime, the same global defines, the same optimization settings, with the same sanitizers enabled and so on and so forth.   
Cmake will make sure that the include directories from lib1, lib2 and lib3 will be added to the include paths and the compiled binaries (if any) are linked to my app and that any specific usage requirements (e.g. minimal cmake level or defines) are correctly propagated up the dependency chain.  
  
If you just add the location of the current include directory and require me to separately compile the libraries and  manually link them to my app, then nothing is won over just using b2 and adding a single `target_include_directories(my_app <path-to-boost>)` to my CMakeLists.txt file - on the contrary the situation is even more complex than before.  
  
> But your PR doesn't do any name mangling. This breaks autolinking, for example.  
  
That is because you don't need autolinking (you'd define BOOST_ALL_NO_LIB in the parent project)  . Cmake adds the appropriate `-l<libname>` options and it knows, what that option is, because in the simplest case (the one I try to support here) it just built the respective library and knows where the compiled artifact is.    
In case you are not aware: CMake workflow usually uses a separate top level build folder per configuration.  
  
> So, for now, you have to build Boost separately, with Boost.Build. You can use CMakeLists.txt in your project to add include directories. You'll have a problem with library directory (which will be just one) and maybe libraries (not sure how that would work in CMake).  
  
If I atomic can be used header-only for the majority of it's use cases, that may be fine, but in general that solution is worse than not having a CMake file at all.  
  
  
> Also, AFAICT, it doesn't handle build dependency feature propagation or any kind of conditional dependencies, like Boost.Build does. Like, for example, linking with kernel32 when building with gcc on Windows or defining macros while building the library.   
  
Again, I was focusing on the simple case of compiling Boost.Atomic as a static library as part of a larger cmake project. Not to replicate all capabilities you have with a b2 build. But if those things are necessary, I can of course add them to the CMakeLists.txt file. CMake has support for all those things.  
  
> Some more advanced trickery is used in larger libraries like Boost.Log, and until there is a reasonable way to support building any Boost library, the way Boost.Build does, I don't want to add half solutions.  
  
Sorry to tell you, but now you don't even have half a solution.   
  
The problem is not to build boost libraries via cmake (some might be more difficult than others of course) but to get moving at all and the only way I currently see is to do it in small, incremental steps, because all attempts to introduce a boost-wide solution in one go have failed so far and not due to technical issues, but rather procedural ones.

---

## Comment 6

> Username: Lastique  
> Created_at: 2018-12-18 19:59:46 UTC  
> Url: https://github.com/boostorg/atomic/pull/20#issuecomment-448350337  

> Sorry to tell you, but now you don't even have half a solution.  
  
Right, that's what I've been telling you before in one of the other PRs. There's a lot more to being able to build Boost than just adding include directories and targets. I was just under impression that this would be enough for your case. If not, I might as well revert the change.

---

## Comment 7

> Username: Mike-Devel  
> Created_at: 2018-12-20 08:56:24 UTC  
> Url: https://github.com/boostorg/atomic/pull/20#issuecomment-448921994  

IIRC, all my PRs so far where on header only libraries- there was nothing else to do than specifying targets,  dependencies and include directories. Also, I wrote at the very beginning on the ML, that I don't intend to handle shared library compilation (except,  where it worked "by accident") or name mangling, because I didn't see any usecase for it in the intended workflow - just as I don't intend to support e.g. installation (which is covered by b2 and the changes pdimov made recently).  
  
My solution is internationally not designed to replace b2, but to complement it, because those efforts stall again and again, and not due to technical difficulties.  
  
That being said, if that is the condition for acceptance, I can easily provide a PR that does define all the macros and supports compilation as a shared library, just as your jam file.

---

## Comment 8

> Username: Lastique  
> Created_at: 2018-12-20 09:56:01 UTC  
> Url: https://github.com/boostorg/atomic/pull/20#issuecomment-448939634  

Sorry, but I'm not interested in maintaining a build system, in addition to Boost.Build, that is not even able to build the library in the way equivalent to Boost.Build (i.e. with all currently supported variants and on all supported target platforms). A full CMake equivalent might be useful. Although I'm still very much not happy about supporting two build systems at the same time, I would accept it with the intention to drop one of them in the hopefully not too distant future.  
  
Regarding name mangling, I'm not sure whether it should be used with CMake or not. I believe, the decision should be universal across all Boost libraries, and I'm not qualified enough to make it. It should also relate with the "CMake config files" that were added by @pdimov recently. Maybe you should discuss it on the ML before implementing a solution (if you're willing to in the first place, of course).  
  
As a side note, just to give some perspective, I generally don't like the idea of injecting foreign libraries (such as Boost) into my downstream projects. Not only this breaks modularity and isolation, this also imposes requirements on these libraries. Like in this case, why should a user's project require a CMake build system in Boost? This "CMake everywhere" mindset is wrong, IMO, and it's really annoying. Note though, that I'm not against CMake as a build system, or switching Boost to CMake in general. I understand the potential benefits that can be achieved with your approach and I'm willing to support it. But not as a goal of itself, but only as a transparent side effect of supporting CMake for building Boost.

---

## Comment 9

> Username: timblechmann  
> Created_at: 2018-12-20 12:48:22 UTC  
> Url: https://github.com/boostorg/atomic/pull/20#issuecomment-448988017  

> I intentionally don't want CMakeLists.txt to be used for building the library as the proposed CMakeLists.txt doesn't handle various build variants.  
  
fwiw, b2 and cmake have quite different architecture. but let's please not make it more difficult to roll out prototype, which can then be re-iterated on ... eventually we may want to be able to have a (coarse or fine-grained) control over the builds, but if we want to have the solution for each and every problem in exactly the same way as b2 solves things, we'll still discuss the buildsystem in 2030. (i just have to think at how stephen kelly gave up on boost/cmake a few years ago)  
  
that said (i'm maintaining an in-house cmake build-system for a large subset of boost), these changes seem to go to the right direction, but i'd suggest to include the header files into the library, so that they will be included in the generated project files ...

---

## Comment 10

> Username: Lastique  
> Created_at: 2018-12-20 13:30:15 UTC  
> Url: https://github.com/boostorg/atomic/pull/20#issuecomment-448999883  

@timblechmann I'm not saying problems have to be solved in CMake exactly as they are solved in Boost.Build. I'm saying they have to be solved.

---

## Comment 11

> Username: Mike-Devel  
> Created_at: 2018-12-20 15:13:02 UTC  
> Updated_at: 2018-12-20 16:14:10 UTC  
> Url: https://github.com/boostorg/atomic/pull/20#issuecomment-449030710  

@Lastique: I updated my branch with support for shared library builds and the compile definitions from the jam file. I didn't add the manual specificaion of the WINNT macro, because that is precisely one of the things I don't want each library to define independently and instead to have consistent across all compilations. And according to the msvc documentation(https://docs.microsoft.com/en-us/cpp/porting/modifying-winver-and-win32-winnt?view=vs-2017),  the current value of 0x500 is invalid for VS2017 anyway. I also omitted the "-lkernel32" flag, because cmake seems to do the right thing there automatically (at least on my system). I assume you should see the updates, if you reopen the PR.  
  
The big advantage of using the same build system for all your dependencies is that the propagation of build flags, dependencies and usage requirements is simple. I'm doing a lot of cross-compiling and making sure that every TU, where a particular header is included is compiled with exactly the same flags and that all TUs are compiled with the correct flags to ensure compatibility can become an absolute hedache if each library uses a different build system and at the same time provides multiple different compilation options (this would be less of a trouble if modern libraries weren't header only monsters with dozens of conditional compilation paths in them). Not to mention if you have to fix something yourself in a 3rd party library and aren’t familiar with the details of their build system.  
  
The fundamental problem with introducing cmake in boost as a replacement for b2 isn’t technical (imho). There have been multiple proofs of concepts that “work”. I can’t say if they all do all the b2 does for each library -  there is only so much you can expect from a single person – but I don’t think anyone questions that boost can be build with cmake in principle (and with some library support it should not be more difficult than with b2).   
  
However, all those attempts stalled and I believe the reason ist that boost as a whole is not able to form a consensus and act upon it on a project wide level on any topic (not just cmake). Most other developments I’ve seen (closing trac, dropping compiler from the test-matrix, adding cmake config files) only happen because a single guy has the necessary access permissions to do something and then just did it. A full, consistent, cmake system however needs support (not just pasive aagreement) from all individual library maintainers and that is apparently not going to happen.  
  
So I'm trying to do the reverse: Step by Step increase the number of projects that support cmake from the bottom up (there are already a number of such projects like beast, hana, gil) without the immediate goal to replace b2 but to provide some additional value to your users (at least to me and my team). I'm providing the minimum cmake skeleton for each library that still serves a practical purpose (scratching my own itch here) while not impose any significant maintenance burden on those library maintainers that don't want to fully go cmake just now. The people who want to embrace cmake e.g. for development can build on top of that (e.g. add unit-testing, add support for installation ...) and maybe some day boost will entirely switch to cmake or allow each library to use their own build system, but in the mean time there is tangable progress.   
  
> Regarding name mangling, I'm not sure whether it should be used with CMake or not. I believe, the decision should be universal across all Boost libraries, and I'm not qualified enough to make it.  
  
It doesn't have to be a yes/no decision. Just as boost can currently build with and without name mangling, we can start with only supporting the unmangled version (which is more natural for cmake) and if desired, name-mangling can be activated as an option later. That is what I'm trying to tell you all the time: You don't have to have a complete master plan for cmake that answers all questions up front. Just make sure you don't block certain options prematurely, which is exactly the design goal behind my minimal cmake files.

---

## Comment 12

> Username: pdimov  
> Created_at: 2018-12-20 15:33:31 UTC  
> Url: https://github.com/boostorg/atomic/pull/20#issuecomment-449037545  

Name mangling doesn't make sense in the add_subdirectory workflow; everything is built from source with the same options, and you can't have more than one variant in CMake.  
  
It might make sense in an install-based workflow, but the proposed addition (correctly) doesn't support installation.

---

## Comment 13

> Username: Lastique  
> Created_at: 2018-12-20 16:03:30 UTC  
> Url: https://github.com/boostorg/atomic/pull/20#issuecomment-449047562  

> I updated my branch with support for shared library builds and the compile definitions from the jam file. I assume you should see the updates, if you reopen the PR.  
  
For some reason I can't reopen it. If you can't either, please, create a new one.  
  
> Name mangling doesn't make sense in the add_subdirectory workflow; everything is built from source with the same options, and you can't have more than one variant in CMake.  
  
That is true, but I don't want this workflow to be the only one possible with the added CMakeLists.txt. In fact, I don't consider that workflow the main goal at all. I want to be able to build Boost using CMake so that it doesn't break when consumed by `find_package` or by manually specifying compiler/linker flags in user's project.

---

## Comment 14

> Username: pdimov  
> Created_at: 2018-12-20 16:12:04 UTC  
> Url: https://github.com/boostorg/atomic/pull/20#issuecomment-449050365  

Defining BOOST_USE_WINDOWS_H is a faithful rendition of build/Jamfile but is almost certainly an ODR violation. This should also be a project-wide macro.  
  
I've never understood the desire to replace `b2 install` with a CMake-based installation. There's not much upside here that I can see; what we have already works. Building Boost as a part of a CMake-based project is another story; we currently don't support that, and those patches aim to add this missing support.

---

## Comment 15

> Username: Lastique  
> Created_at: 2018-12-20 16:23:39 UTC  
> Url: https://github.com/boostorg/atomic/pull/20#issuecomment-449054250  

> Defining BOOST_USE_WINDOWS_H is a faithful rendition of build/Jamfile but is almost certainly an ODR violation. This should also be a project-wide macro.  
  
There are cases when that is not practical.  
  
> I've never understood the desire to replace b2 install with a CMake-based installation.  
  
Well, the Steering Committee announced that we should switch to CMake. And there are people who dislike Boost.Build and would like to switch to CMake instead. At least, that's my understanding.

---

## Comment 16

> Username: pdimov  
> Created_at: 2018-12-20 16:44:43 UTC  
> Url: https://github.com/boostorg/atomic/pull/20#issuecomment-449061332  

Yeah, there are cases when ODR violations aren't practical either.  
  
Re CMake install, the Steering Committee has nothing to do with the concrete question of why would a CMake installation be an improvement. You'd have to cmake configure-build-install 6 times to replicate what b2 install does in one invocation, and CMake doesn't even have built-in support for runtime-link.  
  
I can understand people's desire to build Boost with their custom CMake cross-toolchain, but that's a separate use case.

---

## Comment 17

> Username: Lastique  
> Created_at: 2018-12-20 17:18:46 UTC  
> Url: https://github.com/boostorg/atomic/pull/20#issuecomment-449072029  

Since we're moving to CMake anyway, a CMake-only build system would be an improvement in terms of maintenance burden.

---

## Comment 18

> Username: Mike-Devel  
> Created_at: 2018-12-20 18:31:43 UTC  
> Url: https://github.com/boostorg/atomic/pull/20#issuecomment-449093315  

I'm on my mobile at the moment so I can't check, but wasn't `BOOST_USE_WINDOWS_H` only used in the cpp file? At least that's why I made it private.   
  
But I assume it is also used in other libraries?

---

## Comment 19

> Username: Lastique  
> Created_at: 2018-12-20 19:00:16 UTC  
> Url: https://github.com/boostorg/atomic/pull/20#issuecomment-449101423  

> I'm on my mobile at the moment so I can't check, but wasn't BOOST_USE_WINDOWS_H only used in the cpp file?  
  
Yes, it was. Peter is saying you ideally want to define it in your project and propagate it to all your dependencies.  
  
> But I assume it is also used in other libraries?  
  
Yes, some libraries define it when being built to avoid incompatibilities with Windows SDKs. This is a user-definable macro as well.

---
