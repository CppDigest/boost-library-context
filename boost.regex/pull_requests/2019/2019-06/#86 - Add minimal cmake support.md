# #86 [CMake] Add minimal cmake support [Merged]

> Username: Mike-Devel  
> Created at: 2019-06-16 18:28:08 UTC  
> Updated at: 2020-01-20 18:42:19 UTC  
> Merged at: 2020-01-07 18:47:46 UTC  
> Closed at: 2020-01-07 18:47:46 UTC  
> Url: https://github.com/boostorg/regex/pull/86  

Compiles Boost.Regex ~~(without ICU support)~~ and provides cmake target Boost::regex that other libraries can use in order to link to boost regex.   
No support for installation and unit tests  
  
In order to test this PR, put a cmake file like this: https://github.com/Mike-Devel/BoostMinimalCMake_SupportRepo/blob/master/root_cmake_file/CMakeLists.txt into the boost root directory and then run the following commands from a separate build directory:  
  
    - cmake -DBOOST_REGEX_INCLUDE_EXAMPLES=ON <path-to-boost-root>  
    - cmake --build .  
  
After this, the library will be in `<build-dir>libs/regex/` and the examples which are build (not all regex examples will be build by this PR) in  `<build-dir>/libs/regex/example/snippets`

---

## Comment 1

> Username: Mike-Devel  
> Created_at: 2019-12-08 13:18:54 UTC  
> Url: https://github.com/boostorg/regex/pull/86#issuecomment-562947576  

Ping

---

## Comment 2

> Username: pdimov  
> Created_at: 2019-12-08 16:36:08 UTC  
> Url: https://github.com/boostorg/regex/pull/86#issuecomment-562967166  

BOOST_REGEX_BUILD_EXAMPLES probably, to follow the convention set by BUILD_TESTING etc.

---

## Comment 3

> Username: Mike-Devel  
> Created_at: 2019-12-09 09:24:53 UTC  
> Url: https://github.com/boostorg/regex/pull/86#issuecomment-563141533  

I chose `...INCLUDE...` naming pattern, because cmake doesn't actually build them (just creates the targets) and it is imo more descriptive of what actually happens (we are including the examples subdirectory).   
  
But I'm happy to change it to whatever you prefer.   
  
I think a few of my older PRs have have `BOOST_FOO_INCLUDE_TESTS` btw.. Those should probably get fixed too then. To my shame I have to admit I just didn't know about the `BUILD_TESTING` option (or rather that it was a cmake / ctest provided option).

---

## Comment 4

> Username: pdimov  
> Created_at: 2019-12-09 14:31:56 UTC  
> Url: https://github.com/boostorg/regex/pull/86#issuecomment-563264779  

BOOST_FOO_INCLUDE_TESTS doesn't scale at all; imagine that all libraries had it. How are you going to test the whole Boost from the command line, provide 150 options?  
  
The average number of options a library has should be 0, because it's being multiplied by 150.

---

## Comment 5

> Username: Mike-Devel  
> Created_at: 2019-12-10 09:52:05 UTC  
> Url: https://github.com/boostorg/regex/pull/86#issuecomment-563954807  

> How are you going to test the whole Boost from the command line, provide 150 options?  
  
I think we had this discussion already on slack (or was it a different PR/issue here on github?), but one option is:  
  
https://github.com/Mike-Devel/BoostMinCMake_Support/blob/1bb83ca1704320cb85cf3c2ef0cede64c3ba6320/root_cmake_file/CMakeLists.txt#L80-L82    
  
One of the reasons I put the tests behind an option is that tests often depend on libraries that the library doesn't. So if you create those targets unconditionally but don't download all of boost, you get an error at configure time, even if you never intended to compile/run those particular tests. Personally I believe that anyone, who wants to run the whole boost test harness is much more likely to use the official distribution (with all headers in a single directory) and b2 for years to come. Or do you really see any chance that cmake will replace b2 as the official build system in boost?   
  
But don't worry. I tried to keep those files as minimal as the maintainers would allow me in order to not prematurely establish any patterns that later have to be changed across all of boost. I also hoped BCM would get added soonish to boost and the test script could then use the functions provided by BCM instead of repeating the same logic over and over again. So far I only added testing via cmake to 3 libraries (parameter, ratio and rational).   
  
But back to the topic at hand: Do you still want me to change `BOOST_REGEX_INCLUDE_EXAMPLES` to `BOOST_REGEX_BUILD_EXAMPLES` ? Or should I remove the examples from the PR completely?

---

## Comment 6

> Username: pdimov  
> Created_at: 2019-12-10 14:39:24 UTC  
> Url: https://github.com/boostorg/regex/pull/86#issuecomment-564062476  

I don't have a good answer. On one hand, if we have 100 libraries with examples, ccmake will have 100 "build examples" options for the user to wade through. On the other, you are correct that with a partial checkout, the library may build but the examples not. And while BUILD_EXAMPLES is better here, INCLUDE_TESTS may be a better description if the tests are done in my preferred manner (to build on ctest, not on cmake --build.)  
  
So at the moment I don't think I want you to change anything. Fine as is, for now, I suppose.

---

## Comment 7

> Username: Mike-Devel  
> Created_at: 2019-12-21 08:43:46 UTC  
> Url: https://github.com/boostorg/regex/pull/86#issuecomment-568164238  

So, anything I can do to get this merged?

---

## Comment 8

> Username: pdimov  
> Created_at: 2019-12-21 19:45:31 UTC  
> Url: https://github.com/boostorg/regex/pull/86#issuecomment-568206593  

No idea, it's up to John.

---

## Comment 9

> Username: jzmaddock  
> Created_at: 2019-12-22 11:33:37 UTC  
> Url: https://github.com/boostorg/regex/pull/86#issuecomment-568254028  

OK I think I just have 2 comments:  
  
* Can some minimal documentation be added please?  
* At present there is no way for me to tell whether a change to the library would break these CMake files, so I think we need a minimal test case (probably just building the examples since you've added a CMakeLists.txt for them) added to CI.

---

## Comment 10

> Username: jzmaddock  
> Created_at: 2019-12-22 12:12:04 UTC  
> Url: https://github.com/boostorg/regex/pull/86#issuecomment-568256626  

Looks like I need some hand holding documentation as well, opening the project in Visual Studio I get:  
  
```  
[CMake] CMake Error at D:\data\boost\boost\libs\regex\CMakeLists.txt:16 (add_library):  
1> [CMake]   Target "boost_regex" links to target "Boost::assert" but the target was not  
1> [CMake]   found.  Perhaps a find_package() call is missing for an IMPORTED target, or  
1> [CMake]   an ALIAS target is missing?  
1> [CMake]   
1> [CMake]   
1> [CMake] CMake Error at D:\data\boost\boost\libs\regex\CMakeLists.txt:16 (add_library):  
1> [CMake]   Target "boost_regex" links to target "Boost::concept_check" but the target  
1> [CMake]   was not found.  Perhaps a find_package() call is missing for an IMPORTED  
1> [CMake]   target, or an ALIAS target is missing?  
```  
and dozens more like that.

---

## Comment 11

> Username: Mike-Devel  
> Created_at: 2019-12-22 16:14:06 UTC  
> Url: https://github.com/boostorg/regex/pull/86#issuecomment-568276620  

So far it only works as part of the super project the way I described it at the beginning. Just that @pdimov has already added a cmake file to the root folder on the develop branch. I didn't yet test if it is compatible with that.  
  
I'll try to set aside some time to add documentation and CI tests, but it may take until after Christmas before I can take care of that.

---

## Comment 12

> Username: Mike-Devel  
> Created_at: 2019-12-22 16:19:12 UTC  
> Updated_at: 2019-12-22 22:09:00 UTC  
> Url: https://github.com/boostorg/regex/pull/86#issuecomment-568277216  

For explanation: The cmake script here says that boost_regex depends e.g. on Boost::assert, but doesn't say anything about where to find that dependency. Other than b2, cmake will not automatically look for a root cmake file further up the tree.

---

## Comment 13

> Username: pdimov  
> Created_at: 2019-12-22 18:25:12 UTC  
> Url: https://github.com/boostorg/regex/pull/86#issuecomment-568290473  

Opening in Visual Studio would require that the library supports standalone use (outside of the Boost superproject), and this would require a significantly more involved CMakeLists.txt file, as the dependencies would have to be acquired somehow.  
  
As submitted this only allows `add_subdirectory` use as part of a larger project and assumes that all other Boost dependencies are also included by that larger project.  
  
One such larger project is Boost itself, which has very recently gained a root CMakeLists.txt file. Given that, you can test Regex as part of Boost, and I'm using Boost.Timer as a model library to show how it's done. See f.ex.  
  
https://github.com/boostorg/timer/blob/develop/CMakeLists.txt  
https://github.com/boostorg/timer/blob/develop/test/CMakeLists.txt  
https://github.com/boostorg/timer/blob/develop/.travis.yml#L149-L154  
  
If the library is to be usable with add_subdir outside the superproject, the tests need to be guarded against the absence of BoostTest like this:  
  
https://github.com/boostorg/mp11/blob/develop/test/CMakeLists.txt

---

## Comment 14

> Username: Mike-Devel  
> Created_at: 2019-12-28 13:11:48 UTC  
> Url: https://github.com/boostorg/regex/pull/86#issuecomment-569416019  

Ok, I have added cmake builds to travis, made minor adaptations and added some more comments to the cmake file iteself. Does that look fine to you?

---

## Comment 15

> Username: jzmaddock  
> Created_at: 2020-01-07 18:47:40 UTC  
> Url: https://github.com/boostorg/regex/pull/86#issuecomment-571718851  

Looks fine, merging now...

---

## Comment 16

> Username: Mike-Devel  
> Created_at: 2020-01-08 21:38:38 UTC  
> Url: https://github.com/boostorg/regex/pull/86#issuecomment-572268911  

Thank you very much

---

## Comment 17

> Username: jzmaddock  
> Created_at: 2020-01-20 09:00:53 UTC  
> Url: https://github.com/boostorg/regex/pull/86#issuecomment-576174351  

I was afraid this would happen: CMake tests have started failing and I have no idea why: https://travis-ci.org/boostorg/regex/jobs/639190802

---

## Comment 18

> Username: pdimov  
> Created_at: 2020-01-20 14:45:46 UTC  
> Url: https://github.com/boostorg/regex/pull/86#issuecomment-576305755  

I'll fix it.  
  
Is there a reason to list the `git submodule update` dependencies manually, or can we switch to using depinst?

---

## Comment 19

> Username: jzmaddock  
> Created_at: 2020-01-20 18:13:45 UTC  
> Url: https://github.com/boostorg/regex/pull/86#issuecomment-576383703  

>Is there a reason to list the git submodule update dependencies manually, or can we switch to using depinst?  
  
In the travis script?  It's just faster to run that way and there aren't that many dependencies anyway.  Are there dependencies that are introduced by the CMake code that are not otherwise present in the source?  If so that would seem to be a retrograde step.

---

## Comment 20

> Username: pdimov  
> Created_at: 2020-01-20 18:24:49 UTC  
> Url: https://github.com/boostorg/regex/pull/86#issuecomment-576386680  

The CMake transitive dependencies (nearly always) equal the dependencies depinst computes. It's true that this set is somewhat larger than what was in the manual list. With the help of `--jobs 3` the time taken didn't change.

---

## Comment 21

> Username: pdimov  
> Created_at: 2020-01-20 18:42:18 UTC  
> Url: https://github.com/boostorg/regex/pull/86#issuecomment-576391423  

The fixes are in https://github.com/boostorg/regex/pull/99. If you don't like the use of depinst and prefer we keep the manual list, let me know, I'll prepare an alternative PR.

---
