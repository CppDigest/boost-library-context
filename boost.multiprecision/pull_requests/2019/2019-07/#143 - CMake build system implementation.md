# #143 CMake build system implementation [Open]

> Username: nemothenoone  
> Created at: 2019-07-15 23:05:31 UTC  
> Updated at: 2021-12-22 18:36:03 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/143  

This makes the Boost.Multiprecision to support the CMake build system with following particular qualities:  
- Build time increased up to 2 times (obviously). So for now it could exceed the time limit available for free. Maybe TravisCI build stages (https://docs.travis-ci.com/user/build-stages) are the answer?  
- Some tests are temporarily disabled, they will be introduced a little bit later.  
- Split to test suites in case of CMake-based build system usage is a little bit different for now. This means tests are being run by CTest, which is not configured for test suites support for now, but carefully annotates every test name, so it is still easy to know, what is wrong.  
- CMake-based version uses standard ```find_package``` for Boost lookup, so it is required to install Boost to CI environment before it could be used by CMake. This needs a couple of improvements:  
  - How to make ```bjam``` to build and install only particular libraries? This would allow to reduce the build time and possibly to get fit to the TravisCI time available for free.  
  - Usage of BoostCMake (https://github.com/BoostCMake/boost_cmake) for Boost building is the key for the Boost installation avoidance. But the project linked is not really up to date for now. We will make it so, after that this project's CMake build system will be going to be updated as well as CI script.  
  - Some submoduled project of my own was used for providing fine CMake build system support for Boost-family projects, but, I think, it would be fine to embed its' repository into Boost.Multiprecision's one.  
 - TravisCI is the only CI system available in the initial implementation. Further improvements will bring more.  
  
Resolves #139 and https://github.com/NilFoundation/multiprecision/issues/10.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2019-07-16 07:49:54 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/143#issuecomment-511706594  

Thanks for the PR, but remembering that the maintainer (that's me!) doesn't speak CMake, this looks to be too much at present:  
  
* I don't think it's acceptable to double CI test times - this library already soaks up a heck of a lot of travis/appveyor time - time that gets shared with other libraries.  Plus it's superfluous to run the tests twice.  By testing CMake, all I meant was some minimal tests that verify that a user-project that attempts to find this library gets all the dependencies and options it needs pulled in.  Let's keep it simple please :)  
* There's a bunch of stuff under CMake/modules that looks to be generic code pulled in from somewhere else - including documentation - do we really need all this?  I don't know, I'm just asking.  
* I spotted a few files with non-Boost, in fact probably non-BSL-compatible copyright licence declarations on them.  They would have to be replaced with versions writen by yourself I'm afraid :(  
* While we're on the subject, all the other new files need your copyright and a BSL licence on them.  
  
In short, can we please simplify this a lot - and please don't get too sidetracked from the main objective of improving cpp_int.  
  
Thanks!

---

## Comment 2

> Username: nemothenoone  
> Created_at: 2019-07-16 11:56:18 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/143#issuecomment-511784965  

What have changed since I wrote that message:  
- Doubling the testing time solved with doubling the TravisCI build targets amount. We have a lot of space to grow now. It is still required to split tests to test suites, so I'ill do it now.  
- Why do we still need for testing to be done with CMake-based build. It is, as you mentioned, required to make sure if all the libraries are linked correctly, if they are of the version required, if they don't crash because of the wrong version was found or something like that. Moreover, I think developers, who would use the CMake-based build, would like to know if they got broken something or not in a convenient manner.  
- Yeap, this generic code is some kind of my other project, used to create the convenient CMake-based build for Boost-family projects. I don't think we need all of it, so I'ill trim it.  
- My bad. These non-BSL compliant files - we don't need them. I'ill trim them out too.  
- That is true too. I'ill put it in once I get this CI-integration working.  
  
Of course, we are not about designing a build system here, it is about the library itself.

---

## Comment 3

> Username: NAThompson  
> Created_at: 2019-07-17 12:16:51 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/143#issuecomment-512229210  

Are all these Appveyor jobs interesting? It seems like there is a very long queue of jobs at the moment, most of which have been obviated by newer commits.  
  
BTW, almost all commits should be `[CI SKIP]` unless there's a good reason for it to kick off a CI job.

---

## Comment 4

> Username: nemothenoone  
> Created_at: 2019-07-17 12:50:44 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/143#issuecomment-512240223  

These jobs are taking long time to be done because I've not split tests to suites for CMake-based build, so every CMake job finishes with timeout only.  
  
I'ill fix it and the problem will be gone.

---

## Comment 5

> Username: pabristow  
> Created_at: 2019-07-17 13:10:13 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/143#issuecomment-512247142  

Mea Culpa also - GIT-stress makes me forget.  Sorry.  
  
Any idea what happens if you remember after pressing return and edit the commit?   
  
Does this have the desired effect?

---

## Comment 6

> Username: jzmaddock  
> Created_at: 2019-07-17 17:30:29 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/143#issuecomment-512447266  

I've cancelled the appveyor runs, since this only runs on Travis anyway.  
  
>Any idea what happens if you remember after pressing return and edit the commit?  
  
As long as you haven't pushed the commit you can amend the last commit.  
  
If you've pushed, then you would have to log onto appveyor/travis and cancel the build.

---

## Comment 7

> Username: jzmaddock  
> Created_at: 2019-07-17 17:33:33 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/143#issuecomment-512454644  

I've cancelled travis as well: it has failures and has been running for 18 hours already.  
  
BTW 18 hours for a run is way to long - as I've said already, this library along with Boost.Math are already past the limit of what's neighbourly, we really need to reduce their run times, not double them.

---

## Comment 8

> Username: nemothenoone  
> Created_at: 2019-07-17 19:40:05 UTC  
> Updated_at: 2019-07-17 19:58:15 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/143#issuecomment-512539538  

Okay. Here what I've done.  
  
- I've implemented test suites, so no timeout cases would now occur.  
- Removed all the non-required build system files.  
- Removed non-BSL-compliant files.  
  
Test time. It is really would be fine to reduce it, but this is a task for a separate PR - for now it is all about CMake-testing.

---

## Comment 9

> Username: jzmaddock  
> Created_at: 2019-07-18 08:51:08 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/143#issuecomment-512726500  

I've cancelled the CI builds again as there were failures, and it's completely saturated the CI servers.  So for example this PR in type_traits has been backed up behind these for over 2 days, and would have been for another 2 or 3 if I'd left all these builds running: https://github.com/boostorg/type_traits/pull/130.  
  
Please, we really don't need to duplicate the testing we already have, just enough to make sure that the root CMake file is functional.

---

## Comment 10

> Username: nemothenoone  
> Created_at: 2019-07-18 11:39:20 UTC  
> Updated_at: 2019-07-18 11:42:18 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/143#issuecomment-512780447  

Okay. Here what have changed:  
  
- Removed double testing. I'ill do it only at my organization, not in here.  
- CMake builds are now only verified by correct library lookup, configuration and build. No double testing.  
- Those failures were not failures. Those, as I've figured out, were ```ctest``` trying to build non-existent for the particular suite tests. This also is going to be fixed.

---

## Comment 11

> Username: nemothenoone  
> Created_at: 2019-07-27 20:01:11 UTC  
> Updated_at: 2019-07-27 20:04:39 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/143#issuecomment-515709758  

@jzmaddock All the checks have passed, no double testing, test time increased, but it is only for configuration (CMake is unfortunately slower than bjam). Is there something I can fix, what prevents this PR from merging?

---

## Comment 12

> Username: jzmaddock  
> Created_at: 2019-08-01 18:54:10 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/143#issuecomment-517413892  

OK comments:  
  
* This is still running way too many tests - the last build of this PR ran for 16 hours on travis, compared to 6 hours for the last build of develop.  Frankly even 6 hours is already too long.  
* The new files need copyright and licence.  
* There is still one file with a non-boost licence - I'm sorry to be so pedantic about this, but we really need to replace that file.

---

## Comment 13

> Username: jzmaddock  
> Created_at: 2019-08-02 19:27:08 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/143#issuecomment-517817788  

Fingers crossed... the clang-format PR is now merged.  
  
I'd suggest you make code changes orthogonal to this PR to avoid getting bogged down on the details of this one.  We will need any new tests added to the Jamfile in any case.

---

## Comment 14

> Username: nemothenoone  
> Created_at: 2019-08-03 02:20:07 UTC  
> Updated_at: 2019-08-03 02:25:50 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/143#issuecomment-517886828  

Okay. What I've done:  
  
* Merged develop into this branch.  
* Updated TravisCI configuration to make it only configure the CMake project, no building.  
* Implemented copyright notices for files with absent ones.  
* Rewrote files with non-Boost license to be different enough to  consider them "new".  
  
It is totally okay and I'm glad you are pedantic about licenses - no users would ever look at the library if something is wrong with licensing stuff.

---

## Comment 15

> Username: jzmaddock  
> Created_at: 2019-08-04 09:52:02 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/143#issuecomment-517989235  

I've just cancelled the CI jobs - there's still something very wrong here - 192 separate jobs on Travis alone and it's taking about a full day to build.  Before there were 90 jobs and it took 6 hours.  
  
BTW please try to cancel the appveyor jobs when you push to this - they don't show anything useful as they're not running CMake and there are a bunch of other libraries backed up behind them.

---

## Comment 16

> Username: nemothenoone  
> Created_at: 2019-08-04 18:46:38 UTC  
> Updated_at: 2019-08-04 18:51:38 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/143#issuecomment-518026676  

Well, what is happening with TravisCI:  
  
* There are 192 jobs because there are separate jobs for CMake-based build and for bjam-based build.  
* CMake-based build is separated because it can cause job timeout for bjam-based tests.  
* CMake-based build is slower itself than bjam-based because CMake itself is slower.  
* CMake-based build is slower that bjam-based because it compiles and installs the whole Boost into a test system for the build system to be able to look for it just as for regular library. This is different from bjam-based build because no installation is required for bjam, multiprecision build system just integrates into the Boost's one. CMake has to manage the integration via regular library lookup interface. We could try to reduce this installation a little bit time with compiling only required libraries, but this is kinda 3 minutes speedup.  
* There are ways to make CMake behave the same as bjam-based build do (just integrate into the whole Boost build system), but this requires for the whole Boost itself to be built with CMake. I got such a project (https://github.com/BoostCMake), it was forked and refactored from some famous but outdated one. Such a build system-integration makes it build faster, but since bjam is in charge - we I have to manage the integration in some way.  
* CMake-based build does not even gets built (not even tested) after the last changeset. It gets only configured. I can try to embed the CMake-configuration into regular bjam-based jobs, but this, I repeat, can cause job timeout, so we would need to regroup tests in test suites.  
* Why I still think there is some sense not only to build with CMake, but to test with it. CMake-configuration includes, just as bjam-based one, lots of compiler definitions and options replicating the main build configuration. Some of them could only be figured out to be set correctly with build and testing done. I'm fine with disabling such a build-and-test only for this repository because the maintainer would not use the newly introduced build system.

---

## Comment 17

> Username: nemothenoone  
> Created_at: 2019-08-10 06:37:44 UTC  
> Updated_at: 2019-08-11 07:18:20 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/143#issuecomment-520123253  

Okay. What have changed:  
- CI Jobs amount was reduced to 90, just like it was before.  
- It still take more than 6 hours to build, but, I'm afraid, we have reached the minimum. CMake unfortunately cannot be integrated with bjam build system, so we could avoid boost installation and save about 3-5 minutes for each job. Also, as I've mentioned before, CMake is basically slower, than bjam, so that makes the difference too.  
- No double-testing, no double-building, only CMake configuration validation is done.

---

## Review 18 [Commented]

> Username: jzmaddock  
> Created_at: 2019-08-10 11:12:28 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/143#pullrequestreview-273421858  

📁 .travis.yml

```diff
  59 | 
  60 |+   allow_failures:
  61 |+     - env: TEST_SUITE=compile_fail
```

> Username: jzmaddock  
> Created_at: 2019-08-10 11:12:28 UTC  
> Updated_at: 2020-11-03 07:17:51 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/143#discussion_r312699535  

Does this allow bjam builds to fail as well?  If so it shouldn't do so.

> Username: nemothenoone  
> Created_at: 2019-08-10 11:14:41 UTC  
> Updated_at: 2020-11-03 07:17:51 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/143#discussion_r312699588  

Not, that should not be there. My bad. Removed.


---

## Review 19 [Commented]

> Username: jzmaddock  
> Created_at: 2019-08-10 11:13:06 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/143#pullrequestreview-273421879  

📁 .travis.yml

```diff
 386 |     - os: linux
 330 |-       env: TOOLSET=gcc COMPILER=g++-6 CXXSTD=c++03 TEST_SUITE="compile_fail examples"
 387 |+       env: TOOLSET=gcc COMPILER=g++-6 CXXSTD=c++03 TEST_SUITE="examples compile_fail"
```

> Username: jzmaddock  
> Created_at: 2019-08-10 11:13:06 UTC  
> Updated_at: 2020-11-03 07:17:51 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/143#discussion_r312699559  

Just curious, why the gratuitous change in order?

> Username: nemothenoone  
> Created_at: 2019-08-10 11:15:33 UTC  
> Updated_at: 2020-11-03 07:17:51 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/143#discussion_r312699608  

Nothing special. Could be reverted. Should be no difference. Maybe, that was accidentally changed while crafting.


---

## Review 20 [Commented]

> Username: jzmaddock  
> Created_at: 2019-08-10 11:13:40 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/143#pullrequestreview-273421897  

📁 .travis.yml

```diff
 414 |     - os: linux
 356 |-       env: TOOLSET=gcc COMPILER=g++-6 CXXSTD=c++14 TEST_SUITE="compile_fail examples"
 415 |+       env: TOOLSET=gcc COMPILER=g++-6 CXXSTD=c++14 TEST_SUITE="examples"
```

> Username: jzmaddock  
> Created_at: 2019-08-10 11:13:40 UTC  
> Updated_at: 2020-11-03 07:17:51 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/143#discussion_r312699572  

What happened to the compile_fail test suite in this row?

> Username: nemothenoone  
> Created_at: 2019-08-10 11:17:15 UTC  
> Updated_at: 2020-11-03 07:17:51 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/143#discussion_r312699635  

You are right. That is something, what should be there.


---

## Review 21 [Commented]

> Username: jzmaddock  
> Created_at: 2019-08-12 07:45:13 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/143#pullrequestreview-273543673  

📁 .travis.yml

```diff
1152 |   email:
1033 |-     on_success: always
1153 |+     on_success: always
```

> Username: jzmaddock  
> Created_at: 2019-08-12 07:45:13 UTC  
> Updated_at: 2020-11-03 07:17:51 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/143#discussion_r312806351  

Can I just get you to end this file with a newline - go ahead and let it trigger a CI build this time so I can check the (hopefully) final revision before merging.  Thanks!


---

## Comment 22

> Username: jzmaddock  
> Created_at: 2019-08-12 10:54:51 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/143#issuecomment-520379110  

Some more questions:  
  
1) When I open the root CMakeLists.txt with Visual Studio I see:  
  
```  
1> [CMake] CMake Error at D:/compilers/visual studio/2019/Common7/IDE/CommonExtensions/Microsoft/CMake/CMake/share/cmake-3.14/Modules/FindBoost.cmake:2132 (message):  
1> [CMake]   Unable to find the requested Boost libraries.  
1> [CMake]   
1> [CMake]   Unable to find the Boost header files.  Please set BOOST_ROOT to the root  
1> [CMake]   directory containing Boost or BOOST_INCLUDEDIR to the directory containing  
1> [CMake]   Boost's headers.  
```  
  
Even though Multiprecision is located within the Boost tree.  I don't see such an issue if I open say libs/system/CMakeLists.txt.  
  
2) If I try to open libs/multiprecision/test/CMakeLists.txt then in addition to the above, I also see:  
  
```  
3> [CMake] CMake Error at D:\data\boost\boost\libs\multiprecision\test\CMakeLists.txt:11 (include):  
3> [CMake]   include could not find load file:  
3> [CMake]   
3> [CMake]     CMTest  
3> [CMake]   
3> [CMake]   
```

---

## Comment 23

> Username: jzmaddock  
> Created_at: 2019-08-12 12:13:20 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/143#issuecomment-520399827  

Similar issues trying to build with cygwin:  
  
```  
CMake Warning at /usr/share/cmake-3.6.2/Modules/Platform/CYGWIN.cmake:15 (message):  
  CMake no longer defines WIN32 on Cygwin!  
  
  (1) If you are just trying to build this project, ignore this warning or  
  quiet it by setting CMAKE_LEGACY_CYGWIN_WIN32=0 in your environment or in  
  the CMake cache.  If later configuration or build errors occur then this  
  project may have been written under the assumption that Cygwin is WIN32.  
  In that case, set CMAKE_LEGACY_CYGWIN_WIN32=1 instead.  
  
  (2) If you are developing this project, add the line  
  
    set(CMAKE_LEGACY_CYGWIN_WIN32 0) # Remove when CMake >= 2.8.4 is required  
  
  at the top of your top-level CMakeLists.txt file or set the minimum  
  required version of CMake to 2.8.4 or higher.  Then teach your project to  
  build on Cygwin without WIN32.  
Call Stack (most recent call first):  
  /usr/share/cmake-3.6.2/Modules/CMakeSystemSpecificInformation.cmake:36 (include)  
  CMakeLists.txt  
  
  
CMake Error at CMakeLists.txt:11 (include):  
  include could not find load file:  
  
    CMTest  
  
  
CMake Warning at CMakeLists.txt:13 (find_package):  
  By not providing "FindGMP.cmake" in CMAKE_MODULE_PATH this project has  
  asked CMake to find a package configuration file provided by "GMP", but  
  CMake did not find one.  
  
  Could not find a package configuration file provided by "GMP" with any of  
  the following names:  
  
    GMPConfig.cmake  
    gmp-config.cmake  
  
  Add the installation prefix of "GMP" to CMAKE_PREFIX_PATH or set "GMP_DIR"  
  to a directory containing one of the above files.  If "GMP" provides a  
  separate development package or SDK, be sure it has been installed.  
  
  
CMake Warning at CMakeLists.txt:14 (find_package):  
  By not providing "FindQuadmath.cmake" in CMAKE_MODULE_PATH this project has  
  asked CMake to find a package configuration file provided by "Quadmath",  
  but CMake did not find one.  
  
  Could not find a package configuration file provided by "Quadmath" with any  
  of the following names:  
  
    QuadmathConfig.cmake  
    quadmath-config.cmake  
  
  Add the installation prefix of "Quadmath" to CMAKE_PREFIX_PATH or set  
  "Quadmath_DIR" to a directory containing one of the above files.  If  
  "Quadmath" provides a separate development package or SDK, be sure it has  
  been installed.  
  
  
CMake Warning at CMakeLists.txt:15 (find_package):  
  By not providing "FindTomMath.cmake" in CMAKE_MODULE_PATH this project has  
  asked CMake to find a package configuration file provided by "TomMath", but  
  CMake did not find one.  
  
  Could not find a package configuration file provided by "TomMath" with any  
  of the following names:  
  
    TomMathConfig.cmake  
    tommath-config.cmake  
  
  Add the installation prefix of "TomMath" to CMAKE_PREFIX_PATH or set  
  "TomMath_DIR" to a directory containing one of the above files.  If  
  "TomMath" provides a separate development package or SDK, be sure it has  
  been installed.  
  
  
CMake Warning at CMakeLists.txt:16 (find_package):  
  By not providing "FindMPFR.cmake" in CMAKE_MODULE_PATH this project has  
  asked CMake to find a package configuration file provided by "MPFR", but  
  CMake did not find one.  
  
  Could not find a package configuration file provided by "MPFR" with any of  
  the following names:  
  
    MPFRConfig.cmake  
    mpfr-config.cmake  
  
  Add the installation prefix of "MPFR" to CMAKE_PREFIX_PATH or set  
  "MPFR_DIR" to a directory containing one of the above files.  If "MPFR"  
  provides a separate development package or SDK, be sure it has been  
  installed.  
  
  
CMake Warning at CMakeLists.txt:17 (find_package):  
  By not providing "FindMPFI.cmake" in CMAKE_MODULE_PATH this project has  
  asked CMake to find a package configuration file provided by "MPFI", but  
  CMake did not find one.  
  
  Could not find a package configuration file provided by "MPFI" with any of  
  the following names:  
  
    MPFIConfig.cmake  
    mpfi-config.cmake  
  
  Add the installation prefix of "MPFI" to CMAKE_PREFIX_PATH or set  
  "MPFI_DIR" to a directory containing one of the above files.  If "MPFI"  
  provides a separate development package or SDK, be sure it has been  
  installed.  
  
  
CMake Warning at CMakeLists.txt:18 (find_package):  
  By not providing "FindMPC.cmake" in CMAKE_MODULE_PATH this project has  
  asked CMake to find a package configuration file provided by "MPC", but  
  CMake did not find one.  
  
  Could not find a package configuration file provided by "MPC" with any of  
  the following names:  
  
    MPCConfig.cmake  
    mpc-config.cmake  
  
  Add the installation prefix of "MPC" to CMAKE_PREFIX_PATH or set "MPC_DIR"  
  to a directory containing one of the above files.  If "MPC" provides a  
  separate development package or SDK, be sure it has been installed.  
  
  
CMake Warning at CMakeLists.txt:19 (find_package):  
  By not providing "FindEigen3.cmake" in CMAKE_MODULE_PATH this project has  
  asked CMake to find a package configuration file provided by "Eigen3", but  
  CMake did not find one.  
  
  Could not find a package configuration file provided by "Eigen3" with any  
  of the following names:  
  
    Eigen3Config.cmake  
    eigen3-config.cmake  
  
  Add the installation prefix of "Eigen3" to CMAKE_PREFIX_PATH or set  
  "Eigen3_DIR" to a directory containing one of the above files.  If "Eigen3"  
  provides a separate development package or SDK, be sure it has been  
  installed.  
  
  
CMake Warning at /usr/share/cmake-3.6.2/Modules/FindBoost.cmake:743 (message):  
  Imported targets not available for Boost version 107100  
Call Stack (most recent call first):  
  /usr/share/cmake-3.6.2/Modules/FindBoost.cmake:842 (_Boost_COMPONENT_DEPENDENCIES)  
  /usr/share/cmake-3.6.2/Modules/FindBoost.cmake:1395 (_Boost_MISSING_DEPENDENCIES)  
  CMakeLists.txt:52 (find_package)  
  
  
CMake Warning at /usr/share/cmake-3.6.2/Modules/FindBoost.cmake:743 (message):  
  Imported targets not available for Boost version 107100  
Call Stack (most recent call first):  
  /usr/share/cmake-3.6.2/Modules/FindBoost.cmake:842 (_Boost_COMPONENT_DEPENDENCIES)  
  /usr/share/cmake-3.6.2/Modules/FindBoost.cmake:1395 (_Boost_MISSING_DEPENDENCIES)  
  CMakeLists.txt:52 (find_package)  
  
  
CMake Warning at /usr/share/cmake-3.6.2/Modules/FindBoost.cmake:743 (message):  
  Imported targets not available for Boost version 107100  
Call Stack (most recent call first):  
  /usr/share/cmake-3.6.2/Modules/FindBoost.cmake:842 (_Boost_COMPONENT_DEPENDENCIES)  
  /usr/share/cmake-3.6.2/Modules/FindBoost.cmake:1395 (_Boost_MISSING_DEPENDENCIES)  
  CMakeLists.txt:52 (find_package)  
  
  
CMake Warning at /usr/share/cmake-3.6.2/Modules/FindBoost.cmake:743 (message):  
  Imported targets not available for Boost version 107100  
Call Stack (most recent call first):  
  /usr/share/cmake-3.6.2/Modules/FindBoost.cmake:842 (_Boost_COMPONENT_DEPENDENCIES)  
  /usr/share/cmake-3.6.2/Modules/FindBoost.cmake:1395 (_Boost_MISSING_DEPENDENCIES)  
  CMakeLists.txt:52 (find_package)  
  
  
CMake Warning at /usr/share/cmake-3.6.2/Modules/FindBoost.cmake:743 (message):  
  Imported targets not available for Boost version 107100  
Call Stack (most recent call first):  
  /usr/share/cmake-3.6.2/Modules/FindBoost.cmake:842 (_Boost_COMPONENT_DEPENDENCIES)  
  /usr/share/cmake-3.6.2/Modules/FindBoost.cmake:1395 (_Boost_MISSING_DEPENDENCIES)  
  CMakeLists.txt:52 (find_package)  
  
  
CMake Warning at /usr/share/cmake-3.6.2/Modules/FindBoost.cmake:743 (message):  
  Imported targets not available for Boost version 107100  
Call Stack (most recent call first):  
  /usr/share/cmake-3.6.2/Modules/FindBoost.cmake:842 (_Boost_COMPONENT_DEPENDENCIES)  
  /usr/share/cmake-3.6.2/Modules/FindBoost.cmake:1395 (_Boost_MISSING_DEPENDENCIES)  
  CMakeLists.txt:52 (find_package)  
  
  
CMake Warning at /usr/share/cmake-3.6.2/Modules/FindBoost.cmake:743 (message):  
  Imported targets not available for Boost version 107100  
Call Stack (most recent call first):  
  /usr/share/cmake-3.6.2/Modules/FindBoost.cmake:842 (_Boost_COMPONENT_DEPENDENCIES)  
  /usr/share/cmake-3.6.2/Modules/FindBoost.cmake:1395 (_Boost_MISSING_DEPENDENCIES)  
  CMakeLists.txt:52 (find_package)  
  
  
-- Boost version: 1.71.0  
-- Found the following Boost libraries:  
--   chrono  
--   exception  
--   timer  
--   filesystem  
--   unit_test_framework  
--   serialization  
--   regex  
CMake Error at CMakeLists.txt:55 (cm_test_link_libraries):  
  Unknown CMake command "cm_test_link_libraries".  
  
  
CMake Warning (dev) in CMakeLists.txt:  
  No cmake_minimum_required command is present.  A line of code such as  
  
    cmake_minimum_required(VERSION 3.6)  
  
  should be added at the top of the file.  The version specified may be lower  
  if you wish to support older CMake versions for this project.  For more  
  information run "cmake --help-policy CMP0000".  
This warning is for project developers.  Use -Wno-dev to suppress it.  
  
-- Configuring incomplete, errors occurred!  
See also "/cygdrive/d/data/boost/boost/libs/multiprecision/test/CMakeFiles/CMakeOutput.log".  
```

---

## Comment 24

> Username: nemothenoone  
> Created_at: 2019-08-12 12:30:16 UTC  
> Updated_at: 2019-08-12 15:56:13 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/143#issuecomment-520404957  

Found some minor issues with test headers generation path. Fixed that, so CI is triggered.  
  
1. Simple library emplacement into a Boost repository tree does not make the CMake work well. As I mentioned before, this works for bjam-based build because of Boost.Multiprecision integrates into the whole build-system just as another one target. CMake is not capable to integrate into bjam target tree, so it looks for Boost just as for usual libraries set. This means Boost should be built somewhere (maybe some toolchain) and only after that Boost.Multiprecision could be cmaked with ```-DBOOST_ROOT=/your/path/to/pre/built/boost```.  
  
There is a way for the CMake-based build system to behave itself just like bjam-based does now - simply integrate another one target into the target-tree, but this requires for the whole Boost to be built with CMake, which is a discussable question for the last 10 years. I got a project (https://github.com/BoostCMake/boost-cmake.git) which makes Boost to be buildable with CMake (basically, it is about implementing CMake-based build system for each library and then a superproject, just like VS solution), but the latest version it works for is 1.61.0. I'ill update that and maybe propose in Boost mailing list as something useful. But for now the way I described earlier - is the only one.  
  
Boost.System has no such a problem because of its' CMake configuration is very basic, more like a stub: no testing configuration, no source-files management, no build rules. It is even said, it is a stub: https://github.com/boostorg/system/blob/develop/CMakeLists.txt#L5  
  
2. ```test/CMakeLists.txt``` is not intended to be used in separate from the root ```CMakeLists.txt```. It is a common practice, so folks use options for CMake to enable or disable some features. These options are defined with ```-DOPTION_NAME=TRUE/FALSE``` after ```cmake``` command.  
  
Fine example of such option is an option to enable or disable build with unit tests on. This particular project was made to have an option ```BUILD_UNIT_TESTS```. So you need to generate the project from the root ```CMakeLists.txt``` with ```-DBUILD_UNIT_TESTS=TRUE``` and only after that you can open the result project with VisualStudio, so unit tests will be present.  
  
3. I suggest Cygwin-based configuration you posted was done from ```test/CMakeLists.txt```?

---

## Comment 25

> Username: jzmaddock  
> Created_at: 2019-08-12 15:39:42 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/143#issuecomment-520478024  

>I suggest Cygwin-based configuration you posted was done from test/CMakeLists.txt?  
  
Yep, OK I'm sure I'm missing something but now I see:  
  
```  
cd libs/multiprecision  
CMake -DBUILD_UNIT_TESTS=TRUE .  
```  
  
exits OK, but then  
  
```  
make all  
```  
  
Builds almost nothing:  
  
```  
-- Configuring done  
-- Generating done  
-- Build files have been written to: /cygdrive/d/data/boost/boost/libs/multiprecision  
[100%] Built target no_eh_support  
```  
  
and  
  
```  
make test  
```  
  
Fails with lots of errors like this:  
  
```  
Running tests...  
Test project /cygdrive/d/data/boost/boost/libs/multiprecision  
        Start   1: multiprecision_test_test_arithmetic_backend_concept  
Could not find executable /cygdrive/d/data/boost/boost/libs/multiprecision/test/multiprecision_test_test_arithmetic_backend_concept.exe  
Looked in the following places:  
/cygdrive/d/data/boost/boost/libs/multiprecision/test/multiprecision_test_test_arithmetic_backend_concept.exe  
/cygdrive/d/data/boost/boost/libs/multiprecision/test/multiprecision_test_test_arithmetic_backend_concept.exe.exe  
/cygdrive/d/data/boost/boost/libs/multiprecision/test/Release/multiprecision_test_test_arithmetic_backend_concept.exe  
/cygdrive/d/data/boost/boost/libs/multiprecision/test/Release/multiprecision_test_test_arithmetic_backend_concept.exe.exe  
/cygdrive/d/data/boost/boost/libs/multiprecision/test/Debug/multiprecision_test_test_arithmetic_backend_concept.exe  
/cygdrive/d/data/boost/boost/libs/multiprecision/test/Debug/multiprecision_test_test_arithmetic_backend_concept.exe.exe  
/cygdrive/d/data/boost/boost/libs/multiprecision/test/MinSizeRel/multiprecision_test_test_arithmetic_backend_concept.exe  
/cygdrive/d/data/boost/boost/libs/multiprecision/test/MinSizeRel/multiprecision_test_test_arithmetic_backend_concept.exe.exe  
/cygdrive/d/data/boost/boost/libs/multiprecision/test/RelWithDebInfo/multiprecision_test_test_arithmetic_backend_concept.exe  
/cygdrive/d/data/boost/boost/libs/multiprecision/test/RelWithDebInfo/multiprecision_test_test_arithmetic_backend_concept.exe.exe  
/cygdrive/d/data/boost/boost/libs/multiprecision/test/Deployment/multiprecision_test_test_arithmetic_backend_concept.exe  
/cygdrive/d/data/boost/boost/libs/multiprecision/test/Deployment/multiprecision_test_test_arithmetic_backend_concept.exe.exe  
```  
  
Interestingly, at the end it did try and build a few targets (OK so far as I can tell so far).  
  
What am I doing wrong here?

---

## Comment 26

> Username: nemothenoone  
> Created_at: 2019-08-12 18:35:13 UTC  
> Updated_at: 2019-08-13 05:58:50 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/143#issuecomment-520543790  

First of all, I think it is better not to configure CMake projects inside the source directory - configuration results are usually quite messy. So that would be better to, maybe, create some "build" directory and then configure.  
  
I think, to better understand what is wrong with Windows and Cygwin/MSYS builds, I would need to implement AppVeyor CI integration.

---

## Comment 27

> Username: jzmaddock  
> Created_at: 2019-08-13 09:48:24 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/143#issuecomment-520768180  

I see the same issues on ubuntu (see attached log)  
[out.txt](https://github.com/boostorg/multiprecision/files/3496385/out.txt)

---

## Comment 28

> Username: nemothenoone  
> Created_at: 2019-08-13 10:32:41 UTC  
> Updated_at: 2019-08-14 15:32:44 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/143#issuecomment-520781545  

Well, I got figured out what was wrong.  
  
```tests``` target was not a part of ```all``` target with passing ```-DBUILD_UNIT_TESTS=TRUE```. It was separated.  
  
```BUILD_UNIT_TESTS``` option was implemented as a switcher for CMake to simply include test directories. But the option which was responsible for adding ```tests``` target to ```all``` target was ```BUILD_TESTS```, so the correct way, if you wanted for tests to be built as a part of ```all```, ```cmake -DBUILD_UNIT_TESTS=TRUE -DBUILD_TESTS=TRUE ..```, which is weird.  
  
So I removed ```BUILD_UNIT_TESTS``` option and now ```BUILD_TESTS``` is the only one option responsible for tests.

---

## Comment 29

> Username: jzmaddock  
> Created_at: 2019-08-14 11:33:47 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/143#issuecomment-521209489  

Ubuntu build now works for me, only SNAFU is that BOOST_ROOT has to be a full path (not a relative one) or it doesn't work for some reason.

---

## Comment 30

> Username: jzmaddock  
> Created_at: 2019-08-14 18:06:58 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/143#issuecomment-521355549  

I'm still having trouble with Visual studio integration (or more specifically with targeting msvc in general).  I see a number of errors when configuring, starting with:  
  
```  
1> [CMake]   test/CMakeLists.txt:52 (find_package)  
1> [CMake] CMake Error at D:/compilers/visual studio/2019/Common7/IDE/CommonExtensions/Microsoft/CMake/CMake/share/cmake-3.14/Modules/FindBoost.cmake:2132 (message):  
1> [CMake]   Unable to find the requested Boost libraries.  
1> [CMake]   
1> [CMake]   Boost version: 1.71.0  
1> [CMake]   
1> [CMake]   Boost include path: D:/data/boost/boost  
1> [CMake]   
1> [CMake]   Could not find the following Boost libraries:  
1> [CMake]   
1> [CMake]           boost_exception  
1> [CMake]   
1> [CMake]   Some (but not all) of the required Boost libraries were found.  You may  
1> [CMake]   need to install these additional Boost libraries.  Alternatively, set  
1> [CMake]   BOOST_LIBRARYDIR to the directory containing Boost libraries or BOOST_ROOT  
1> [CMake]   to the location of Boost.  
1> [CMake] Call Stack (most recent call first):  
1> [CMake]   test/CMakeLists.txt:52 (find_package)  
```  
  
Now the strange thing is, I did build Boost.Exception and it's library files are present and no differently named to any others:  
  
```  
$ ls ../../../stage/lib  
boost_chrono-vc141-mt-gd-x64-1_71.dll               libboost_exception-vc141-mt-gd-x64-1_71.lib  
boost_chrono-vc141-mt-gd-x64-1_71.lib               libboost_exception-vc141-mt-sgd-x64-1_71.lib  
boost_chrono-vc141-mt-x64-1_71.dll                  libboost_exception-vc141-mt-s-x64-1_71.lib  
boost_chrono-vc141-mt-x64-1_71.lib                  libboost_exception-vc141-mt-x64-1_71.lib  
boost_filesystem-vc141-mt-gd-x64-1_71.dll           libboost_filesystem-vc141-mt-gd-x64-1_71.lib  
boost_filesystem-vc141-mt-gd-x64-1_71.lib           libboost_filesystem-vc141-mt-sgd-x64-1_71.lib  
boost_filesystem-vc141-mt-x64-1_71.dll              libboost_filesystem-vc141-mt-s-x64-1_71.lib  
boost_filesystem-vc141-mt-x64-1_71.lib              libboost_filesystem-vc141-mt-x64-1_71.lib  
boost_prg_exec_monitor-vc141-mt-gd-x64-1_71.dll     libboost_prg_exec_monitor-vc141-mt-gd-x64-1_71.lib  
boost_prg_exec_monitor-vc141-mt-gd-x64-1_71.lib     libboost_prg_exec_monitor-vc141-mt-sgd-x64-1_71.lib  
boost_prg_exec_monitor-vc141-mt-x64-1_71.dll        libboost_prg_exec_monitor-vc141-mt-s-x64-1_71.lib  
boost_prg_exec_monitor-vc141-mt-x64-1_71.lib        libboost_prg_exec_monitor-vc141-mt-x64-1_71.lib  
boost_random-vc141-mt-gd-x64-1_71.dll               libboost_random-vc141-mt-gd-x64-1_71.lib  
boost_random-vc141-mt-gd-x64-1_71.lib               libboost_random-vc141-mt-sgd-x64-1_71.lib  
boost_random-vc141-mt-x64-1_71.dll                  libboost_random-vc141-mt-s-x64-1_71.lib  
boost_random-vc141-mt-x64-1_71.lib                  libboost_random-vc141-mt-x64-1_71.lib  
boost_regex-vc141-mt-gd-x64-1_71.dll                libboost_regex-vc141-mt-gd-x64-1_71.lib  
boost_regex-vc141-mt-gd-x64-1_71.lib                libboost_regex-vc141-mt-sgd-x64-1_71.lib  
boost_regex-vc141-mt-x64-1_71.dll                   libboost_regex-vc141-mt-s-x64-1_71.lib  
boost_regex-vc141-mt-x64-1_71.lib                   libboost_regex-vc141-mt-x64-1_71.lib  
boost_serialization-vc141-mt-gd-x64-1_71.dll        libboost_serialization-vc141-mt-gd-x64-1_71.lib  
boost_serialization-vc141-mt-gd-x64-1_71.lib        libboost_serialization-vc141-mt-sgd-x64-1_71.lib  
boost_serialization-vc141-mt-x64-1_71.dll           libboost_serialization-vc141-mt-s-x64-1_71.lib  
boost_serialization-vc141-mt-x64-1_71.lib           libboost_serialization-vc141-mt-x64-1_71.lib  
boost_system-vc141-mt-gd-x64-1_71.dll               libboost_system-vc141-mt-gd-x64-1_71.lib  
boost_system-vc141-mt-gd-x64-1_71.lib               libboost_system-vc141-mt-sgd-x64-1_71.lib  
boost_system-vc141-mt-x64-1_71.dll                  libboost_system-vc141-mt-s-x64-1_71.lib  
boost_system-vc141-mt-x64-1_71.lib                  libboost_system-vc141-mt-x64-1_71.lib  
boost_timer-vc141-mt-gd-x64-1_71.dll                libboost_test_exec_monitor-vc141-mt-gd-x64-1_71.lib  
boost_timer-vc141-mt-gd-x64-1_71.lib                libboost_test_exec_monitor-vc141-mt-sgd-x64-1_71.lib  
boost_timer-vc141-mt-x64-1_71.dll                   libboost_test_exec_monitor-vc141-mt-s-x64-1_71.lib  
boost_timer-vc141-mt-x64-1_71.lib                   libboost_test_exec_monitor-vc141-mt-x64-1_71.lib  
boost_unit_test_framework-vc141-mt-gd-x64-1_71.dll  libboost_timer-vc141-mt-gd-x64-1_71.lib  
boost_unit_test_framework-vc141-mt-gd-x64-1_71.lib  libboost_timer-vc141-mt-sgd-x64-1_71.lib  
boost_unit_test_framework-vc141-mt-x64-1_71.dll     libboost_timer-vc141-mt-s-x64-1_71.lib  
boost_unit_test_framework-vc141-mt-x64-1_71.lib     libboost_timer-vc141-mt-x64-1_71.lib  
boost_wserialization-vc141-mt-gd-x64-1_71.dll       libboost_unit_test_framework-vc141-mt-gd-x64-1_71.lib  
boost_wserialization-vc141-mt-gd-x64-1_71.lib       libboost_unit_test_framework-vc141-mt-sgd-x64-1_71.lib  
boost_wserialization-vc141-mt-x64-1_71.dll          libboost_unit_test_framework-vc141-mt-s-x64-1_71.lib  
boost_wserialization-vc141-mt-x64-1_71.lib          libboost_unit_test_framework-vc141-mt-x64-1_71.lib  
libboost_chrono-vc141-mt-gd-x64-1_71.lib            libboost_wserialization-vc141-mt-gd-x64-1_71.lib  
libboost_chrono-vc141-mt-sgd-x64-1_71.lib           libboost_wserialization-vc141-mt-sgd-x64-1_71.lib  
libboost_chrono-vc141-mt-s-x64-1_71.lib             libboost_wserialization-vc141-mt-s-x64-1_71.lib  
libboost_chrono-vc141-mt-x64-1_71.lib               libboost_wserialization-vc141-mt-x64-1_71.lib  
```  
  
What I haven't been able to find is where the error is coming from and logged - any ideas?  How do I find out what CMake is actually looking for?  
  
I then see:  
  
```  
1> [CMake] CMake Error: The following variables are used in this project, but they are set to NOTFOUND.  
1> [CMake] -- Configuring incomplete, errors occurred!  
1> [CMake] See also "D:/data/boost/boost/libs/multiprecision/out/build/x64-Debug/CMakeFiles/CMakeOutput.log".  
1> [CMake] Please set them or make sure they are set and tested correctly in the CMake files:  
1> [CMake] GMP_LIBRARIES (ADVANCED)  
1> [CMake]     linked by target "multiprecision_test_test_rational_io_cpp_int" in directory D:/data/boost/boost/libs/multiprecision/test  
1> [CMake]   
1> [CMake] See also "D:/data/boost/boost/libs/multiprecision/out/build/x64-Debug/CMakeFiles/CMakeError.log".  
1> [CMake]   
```  
  
Has `GMP_LIBRARIES` been made non-optional somewhere?  
  
The actual command line was:  
  
```  
D:\COMPILERS\VISUAL STUDIO\2019\COMMON7\IDE\COMMONEXTENSIONS\MICROSOFT\CMAKE\CMake\bin\cmake.exe  -G "Visual Studio 16 2019" -A x64 -DCMAKE_INSTALL_PREFIX:PATH="D:\data\boost\boost\libs\multiprecision\out\install\x64-Debug" -DBUILD_TESTS:BOOL="True" -DBOOST_ROOT=d:\data\boost\boost -DCMAKE_CONFIGURATION_TYPES="Debug" "D:\data\boost\boost\libs\multiprecision"  
```

---

## Comment 31

> Username: nemothenoone  
> Created_at: 2019-08-14 18:18:55 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/143#issuecomment-521359745  

Yeah, I've found ```GMP_LIBRARIES``` being unnecessarily defined for ```test_rational_io``` for the ```CPP_INT``` case. Fixed.  
  
I'm still investigating what is wrong with VS/MinGW/Cygwin/MSYS builds.  
  
What is interesting - ```FindBoost.cmake``` seems to have troubles with looking for ```x64```-postfixed libraries. I really hope I'm wrong with this.

---

## Comment 32

> Username: nemothenoone  
> Created_at: 2019-09-14 03:38:55 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/143#issuecomment-531444963  

Well, looks like we have figured out what was wrong with the Windows-based configuration. Working example of the project being configured with CMake on Windows is present in AppVeyor configuration now.

---

## Comment 33

> Username: jzmaddock  
> Created_at: 2019-09-16 08:00:56 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/143#issuecomment-531677326  

This:  
  
```  
C:\projects\boost-root\boost/math/tools/complex.hpp(20): error C2953: 'boost::math::tools::is_complex_type': class template has already been defined  
```  
  
Is caused by an outdated Boost.Math (it needs latest develop).  Have you changed the way dependencies are pulled down because this was working OK in the last develop build: https://ci.appveyor.com/project/jzmaddock/multiprecision/builds/27344996

---

## Comment 34

> Username: nemothenoone  
> Created_at: 2019-09-17 18:32:22 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/143#issuecomment-532345848  

Okay. I got updated the .appveyor.yml to use ```develop``` branch by default, until next Boost major release this seems to be a solution. After the release I'ill change that back.

---

## Comment 35

> Username: nemothenoone  
> Created_at: 2019-10-15 20:47:32 UTC  
> Updated_at: 2019-10-15 20:47:38 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/143#issuecomment-542397675  

For now I see no CMake-dependent build failures. Only ```serialization::make_array```-related ones. This seems to be coming from ```develop```.

---

## Comment 36

> Username: nemothenoone  
> Created_at: 2019-11-03 06:18:46 UTC  
> Updated_at: 2019-11-03 06:20:09 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/143#issuecomment-549108880  

@jzmaddock Updated and works fine. What do you think?

---

## Comment 37

> Username: pdimov  
> Created_at: 2019-12-12 18:04:15 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/143#issuecomment-565118348  

I'm not quite sure that the copyright/license on FindPackageHandleStandardArgs.cmake and FindPackageMessage.cmake (at least) are correct. These are parts of CMake:  
  
https://github.com/Kitware/CMake/blob/master/Modules/FindPackageHandleStandardArgs.cmake  
https://github.com/Kitware/CMake/blob/master/Modules/FindPackageMessage.cmake  
  
and are distributed under the BSD 3-clause license, which cannot be relicensed under BSL. And even if it could, the copyright owners would be wrong (unless you wrote the files included with CMake?)

---

## Comment 38

> Username: nemothenoone  
> Created_at: 2019-12-12 20:30:55 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/143#issuecomment-565173991  

I'm not an author of these files and none from my organization is. Relicensing is also not available in this case. That is definitely a problem I missed during review.  
  
I'ill replace these files by myself in some time.

---

## Comment 39

> Username: nemothenoone  
> Created_at: 2020-03-06 23:57:59 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/143#issuecomment-596013472  

Okay. One more try to get this approved. Seems like build works fine with no license-issued files.

---

## Comment 40

> Username: jzmaddock  
> Created_at: 2020-03-09 16:56:28 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/143#issuecomment-596650553  

I'm seeing this on both cygwin and WSL - what am I doing wrong?  
  
```  
.  
-- The C compiler identification is GNU 7.4.0  
-- The CXX compiler identification is GNU 7.4.0  
-- Check for working C compiler: /usr/bin/cc  
-- Check for working C compiler: /usr/bin/cc -- works  
-- Detecting C compiler ABI info  
-- Detecting C compiler ABI info - done  
-- Detecting C compile features  
-- Detecting C compile features - done  
-- Check for working CXX compiler: /usr/bin/c++  
-- Check for working CXX compiler: /usr/bin/c++ -- works  
-- Detecting CXX compiler ABI info  
-- Detecting CXX compiler ABI info - done  
-- Detecting CXX compile features  
-- Detecting CXX compile features - done  
CMake Error at cmake/packages/FindBoost.cmake:1496 (cmake_policy):  
  cmake_policy GET given policy "CMP0074" which is not known to this version  
  of CMake.  
Call Stack (most recent call first):  
  CMakeLists.txt:78 (find_package)  
  
  
CMake Error at cmake/packages/FindBoost.cmake:1649 (cmake_policy):  
  cmake_policy GET must be given exactly 2 additional arguments.  
Call Stack (most recent call first):  
  CMakeLists.txt:78 (find_package)  
  
  
CMake Warning at cmake/packages/FindBoost.cmake:1150 (message):  
  New Boost version may have incorrect or missing dependencies and imported  
  targets  
Call Stack (most recent call first):  
  cmake/packages/FindBoost.cmake:1272 (_Boost_COMPONENT_DEPENDENCIES)  
  cmake/packages/FindBoost.cmake:1910 (_Boost_MISSING_DEPENDENCIES)  
  CMakeLists.txt:78 (find_package)  
  
  
CMake Error at /usr/share/cmake-3.10/Modules/FindPackageHandleStandardArgs.cmake:137 (message):  
  Could NOT find Boost (missing: random) (found version "1.73.0")  
Call Stack (most recent call first):  
  /usr/share/cmake-3.10/Modules/FindPackageHandleStandardArgs.cmake:378 (_FPHSA_FAILURE_MESSAGE)  
  cmake/packages/FindBoost.cmake:2167 (find_package_handle_standard_args)  
  CMakeLists.txt:78 (find_package)  
  
  
-- Configuring incomplete, errors occurred!  
See also "/mnt/d/data/boost/boost/libs/multiprecision/cmake-test/CMakeFiles/CMakeOutput.log".  
```  
  
CMake version is 3.10.2.

---

## Comment 41

> Username: jzmaddock  
> Created_at: 2020-03-09 19:45:04 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/143#issuecomment-596744204  

Googling suggests this may be a cmake -3.15 ism?

---

## Comment 42

> Username: jzmaddock  
> Created_at: 2020-03-09 19:48:44 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/143#issuecomment-596745737  

And.... running the cmake windows GUI, configuration fails for me with missing boost.exception: the only difference from other libraries I can see is that exception does not build a dll, only a lib.

---

## Comment 43

> Username: nemothenoone  
> Created_at: 2020-03-10 01:15:40 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/143#issuecomment-596852896  

Okay. Custom ```FindBoost.cmake``` removal should resolve those troubles with ```boost.exception``` not found along with missing CMake 3.15 policies definitions.

---

## Comment 44

> Username: jzmaddock  
> Created_at: 2020-03-11 16:08:10 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/143#issuecomment-597722738  

Linux build fixed and working nicely - thanks!  
  
Windows still complains about missing exception :(  
  
This seems like an upstream bug to me (in CMake's FindBoost)?  If so I can't help but feel that we're too far ahead of the curve here and wait for CMake (or Boost) to catch up.  @pdimov do you have a view on this?  What's the current status and future of Boost's CMake-ification?  I still worry that this is too much...  
  
None of which should detract from the main task in hand which is modular arithmetic!

---

## Comment 45

> Username: pdimov  
> Created_at: 2020-03-11 23:22:58 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/143#issuecomment-597928499  

What is the purpose of this proposed addition? Standalone use of Boost.Multiprecision against a system-installed Boost?

---

## Comment 46

> Username: nemothenoone  
> Created_at: 2020-03-12 05:00:31 UTC  
> Updated_at: 2020-03-12 06:07:21 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/143#issuecomment-598007563  

@jzmaddock Okay, back to debugging. I actually don't care if it is an upstream bug or not. It should be fixed anyway.  
  
@pdimov There are several reasons I push this:  
1. Development convenience reasons. CMake is being used much wider among developers than bjam, so there are more development tools for that. I personally use CMake-based toolchain for C++ projects and, as much as the organization on behalf I do these pull requests uses it too and has strong intention to put several improvements into Boost.Multiprecision (why write own or use something third-party, when you can improve what is already in Boost?). So this build system implementation is the first step. Modular adaptor is the second one. More are coming.  
2. Boost-improvement reasons. I recently put up a conversation in mailing list about this particular intention, but gave no particular link to the actual code. So watch the Boost mailing list and you will see :)  
3. Boost CMake-fication reasons. I have a project which intends to provide proper CMake build system for Boost with no requirements to maintain it inside particular repositories. But that would be great if some would already have some compatible build system configurations. I intend to make Boost.Multiprecision such one.

---

## Comment 47

> Username: pabristow  
> Created_at: 2020-03-12 10:06:51 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/143#issuecomment-598105005  

Although those who have invested time and effort into using b2 are sad to see that CMake has won the battle, we still don't have CMake working fully to build Boost just as b2 does.  
  
So we are very grateful for someone who knows CMake well has taken on the task.  The time it has taken you to get Boost.Multiprecision working suggests that Cmake-Novices would have struggled.  Boost.Math, and many other libraries are harder and/or more interlinked, so a full CMake build for Boost is still some way off?  
  
(My take-away on this is that it is not enough to produce good software - you have to document it helpfully, explain it endlessly, and sell it, especially to the masses.  bjam/b2 failed.)

---

## Comment 48

> Username: nemothenoone  
> Created_at: 2020-05-03 15:40:26 UTC  
> Updated_at: 2020-05-23 16:11:02 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/143#issuecomment-623128998  

@jzmaddock Okay, one more try. I believe we've fixed the missing Boost.Exception issue.

---

## Comment 49

> Username: nemothenoone  
> Created_at: 2020-05-27 12:23:10 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/143#issuecomment-634623697  

Tested it once again. I believe, we are good for you to test it, @jzmaddock.

---
