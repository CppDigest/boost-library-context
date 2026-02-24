# #168 - Move operations only relevant for test out of main CMakeLists.txt [Open]

> Username: Mike-Devel  
> Created at: 2018-11-12 12:45:13 UTC  
> Updated at: 2019-01-11 20:59:02 UTC  
> Url: https://github.com/boostorg/gil/issues/168  

Just a comment based on my person preference and experience:  
  
I'd recommend moving all the compiler-flags and other machinery that are not necessary for using the library itself into the respective CMakeLists.txt files where they are actually needed.  
  
This is more relevant, when you provide a cmake target (see https://github.com/boostorg/gil/issues/167) and/or have to compile your library, but even if not, I generally prefer to put compiler options next to the targets they are actually relevant for. E.g. `Boost::unit_test_framework` is afaik only needed when you want to run the unit-tests. So it imho should only be looked up when `gil/test/CMakeLists.txt` gets actually included (e.g. not if `GIL_BUILD_TESTS == OFF`). Same with things like `/Wall` or `CMAKE_CXX_STANDARD 11`.   
  
Even if you are not defining a cmake library target (for which you should definetifely not set public warning level flags) it makes it easier for me to discover, which flags I have to set in my project when I want to use this library, vs what flags you are using in order to find bugs in the implementation.

---

## Comment 1

> Username: mloskot  
> Created at: 2018-11-12 13:22:24 UTC  
> Url: https://github.com/boostorg/gil/issues/168#issuecomment-437877776  

First, I should emphasize [the provided CMake configuration allows a couple of ways to **_develop_** Boost.GIL](https://github.com/boostorg/gil/blob/develop/CONTRIBUTING.md#using-cmake), and not to use Boost.GIL.  
  
The CMake configuration should be considered purely _internal_ and as such it has started as a quick hack, gradually becoming more complex than initially intended.  
  
As author of this CMake configuration, I eventually aim to thoroughly modernise it (you can review my PR proposing [modern CMake configuration for GEOS library](https://github.com/libgeos/geos/pull/130) to have an overview of what kind of changes I'm going to make.  
  
More specifically, there will be `INTERFACE` targets with specific compilation flags for building in various development modes (eg. tests, UBSAN, warnings off, etc.).  
  
I am also going to add Boost and GIL targets and link tests, examples against those. Howeer, since this is internal CMake configuration dedicated for Boost.GIL _developers only_, and not consumers, there will be no `Boost::gil`. That is, I will not pollute `Boost::` namespace which:  
  
- is already used by `FindBoost.cmake`  
- may completely change, in both, CMake itself and Boost, with the advent of new CMake proposal for Boost (I'm sure you are very well aware of the ongoing initiative supervised by @robertramey).  
- so I'm not going to make any public, consumer-oriented CMake configuration for Boost.GIL until CMake for Boost itself has not yet settled.  
  
Thank you for your comments, I will keep the issues open as a reminder for future improvements.

---

## Comment 2

> Username: Mike-Devel  
> Created at: 2018-11-12 14:42:03 UTC  
> Updated at: 2018-11-12 14:42:23 UTC  
> Url: https://github.com/boostorg/gil/issues/168#issuecomment-437905878  

Just to clarify: I opened these issues exactly because I was hacking on gil (more precisely because I want to compile some boost libraries with a custom toolchain for a semi-embedded target) which is why I didn't say anything about installation. Having a target you can link against is not only helpful for external consumers, but also when creating your test or example applications. The Idea for `Boost::gil` actually came from the video you linked, but I'd already be happy with a plain `boost_gil` or somesuch.  
  
But I completely understand if - in light of robert's initiative - you don't want to spend time refactoring the cmake files just to change them again, once a boost-wide solution comes up.  
  
> More specifically, there will be INTERFACE targets with specific compilation flags for building in various development modes (eg. tests, UBSAN, warnings off, etc.).  
  
That is something I'd advise against. Let the toolchain file decide if a targets should be compiled with ubsan, against which standard library to link, what optimization flags to use, what compiler and c++ version to use a.s.o.. For a header only library, the only thing your targets needs to specify are dependencies and flags that are purely specific to your library (can't think of a good example right now).

---

## Comment 3

> Username: mloskot  
> Created at: 2018-11-12 16:53:04 UTC  
> Updated at: 2018-11-12 16:54:54 UTC  
> Url: https://github.com/boostorg/gil/issues/168#issuecomment-437952681  

OK, thanks for the clarification  
  
> Having a target you can link against is not only helpful for external consumers  
  
For the time being, I do not want to breed or encourage any external customers of Boost.GIL's CMake. And, I'm going to quite die hard for it, for the time being.  
  
If one wants to use Boost.GIL with a project based on CMake, one needs to stick to `FindBoost.cmake` or own solution.  
  
>>  More specifically, there will be INTERFACE targets with specific compilation flags for building in various development modes (eg. tests, UBSAN, warnings off, etc.).  
>  
> That is something I'd advise against.   
  
I disagree. Use of flags-only targets to manage variety of developer mode builds is not a breach of any (modern) CMake best practices. On the contrary, Kitware uses this technique itself (e.g. VTK-m).   
  
> For a header only library, the only thing your targets needs to specify are dependencies and flags that are purely specific to your library  
  
That is exactly what I'm going to do. Those targets will provide "flags that are purely specific to your library. You seem to be missing my point. The CMake configuration is  **_exclusively_** for those who want to develop and test Boost.GIL, and submit pull request, and they prefer CMake over Boost.Build.  
  
Please, let's stop discussing rules of public consumer-oriented CMake configuration for a library in Boost.GIL. There is no such thing to discuss. And no, I will not accept any PRs with CMake changes that introduce anything for Boost.GIL consumers.  
  
Certainly, this position may change ones Robert's initiative if completed.

---

## Comment 4

> Username: Mike-Devel  
> Created at: 2018-11-12 18:24:49 UTC  
> Url: https://github.com/boostorg/gil/issues/168#issuecomment-437981776  

> Please, let's stop discussing rules of public consumer-oriented CMake configuration for a library in Boost.GIL.   
  
Well the thing with OS software is that your consumers are sometimes/often working on the library themselves (such as me). As I said (in the part you didn't quote) the changes suggested here and in the other issue are useful for the development of this library. But anyway, I absolutely understand you only want to provide cmake files for internal development and I also understand that I should keep my changes to myself - no worries.    
  
> Use of flags-only targets to manage variety of developer mode builds is not a breach of any (modern) CMake best practices.  
  
Did I say it is a breach of any best practices (well actually, I sometimes feel like if you ask ten different people about best practices in cmake you get eleven different answers but that is another topic)?  
  
It has simply been my experience that it makes development and testing much easier, if the library itself doesn't have to worry about all kinds of possible compilation modes and toolchains, because it is something you have to do over and over again for each lib (IIRC that is actually also something that is mentioned in the talk you linked to).   
Instead, you can have a set of toolchain files with the configurations relevant to you (compiler(-version), standdard library (version and flavor), sanitizers, link type for runtime library, static analysis tools etc. ) and then you can compile and run the tests of any new cmake-based library you are developing/using without littering those CMakeFiles.txt with dozens of differently configured targets (or even different package management systems).  
  
Again, just my personal experience - obviously nothing you have to agree with.

---

## Comment 5

> Username: mloskot  
> Created at: 2018-11-12 20:58:33 UTC  
> Url: https://github.com/boostorg/gil/issues/168#issuecomment-438026315  

@Mike-Devel I appreciate your comments. It's always good to be provoked to question and re-think own bits. I never say never. Meanwhile, let's keep this open.  
  
(TBH, at this stage of my love and hate relation to CMake, the toolchain files I hate ;-))

---

## Comment 6

> Username: mloskot  
> Created at: 2019-01-11 20:59:01 UTC  
> Url: https://github.com/boostorg/gil/issues/168#issuecomment-453654544  

I've labelled this as _work-in-progress_ as this idea is not dropped, but postponed until the CMake for Boost has been completed.  
Apparently, some interesting CMake-ification efforts are ongoing thanks to @Mike-Devel & @pdimov, so chances are the GIL's own CMake configuration will need to be modified anyway. Let's wait and see.
