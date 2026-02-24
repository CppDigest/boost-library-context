# #107 Support standalone builds and clean up `test/CMakeLists.txt` [Merged]

> Username: chandryan  
> Created at: 2025-09-18 07:16:53 UTC  
> Updated at: 2025-09-28 12:41:18 UTC  
> Merged at: 2025-09-27 19:01:00 UTC  
> Closed at: 2025-09-27 19:01:00 UTC  
> Url: https://github.com/boostorg/msm/pull/107  

Applied changes:  
  
- Introduced a check whether MSM is built as part of the super-project or standalone in the root CMakeLists.txt  
- Removed/fixed the code related to building the tests standalone in the test CMakeLists.txt  
- Added the missing dependency to the CMake `tests` target  
  
Resolves https://github.com/boostorg/msm/issues/111

---

## Review 1 [Commented]

> Username: Flamefire  
> Created_at: 2025-09-18 08:03:31 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/msm/pull/107#pullrequestreview-3237948636  

Should work, just a missed indent.  
  
What exactly is `BOOST_MSM_NONSTANDALONE_TEST` used for? It doesn't seem to be required for the B2 based build so why for the CMake build?  
  
  
> The intention was the former: Calling cmake from msm, though I just tried out building the tests as part of the super-project and it works well for me - I'll stick to that in the future.  
> [...]  
> Despite my need of running the tests standalone having vanished, I guess the option to build MSM standalone might be useful.  
  
  
Doesn't hurt I guess, as the additional code is small.    
But it might be better to just support the integrated/super-project build unless you are reasonably sure MSM doesn't (and will not) rely on any Boost features introduced after 1.66.  
  
Also supporting 3 types of testing (CMake super-project, standalone, b2) might be a maintenance burden. But that's up to you of course.  
  
Anyway this change looks sound and is certainly an improvement.

📁 CMakeLists.txt

```diff
  34 |+       Boost::type_traits
  35 |+       Boost::typeof
  36 | )
```

> Username: Flamefire  
> Created_at: 2025-09-18 07:55:52 UTC  
> Updated_at: 2025-09-18 08:03:31 UTC  
> Url: https://github.com/boostorg/msm/pull/107#discussion_r2357926481  

```diff  
+  )  
```


---

## Comment 2

> Username: chandryan  
> Created_at: 2025-09-18 15:40:52 UTC  
> Url: https://github.com/boostorg/msm/pull/107#issuecomment-3308193563  

> What exactly is `BOOST_MSM_NONSTANDALONE_TEST` used for? It doesn't seem to be required for the B2 based build so why for the CMake build?  
   
The test Jamfile creates an executable for each source file, in this case BOOST_TEST_MODULE is not defined and a main function is provided in each source file by defining `BOOST_TEST_MODULE`.  
For CMake I have added a separate `main.cpp` and defined `BOOST_MSM_NONSTANDALONE_TEST`, since all tests run in one executable.  
  
  
>   
> Also supporting 3 types of testing (CMake super-project, standalone, b2) might be a maintenance burden. But that's up to you of course.  
>   
  
Indeed, I would like to retain the CMake version due to better IDE integration in VS Code. I'll check on that.

---

## Comment 3

> Username: Flamefire  
> Created_at: 2025-09-18 16:37:39 UTC  
> Url: https://github.com/boostorg/msm/pull/107#issuecomment-3308474174  

> > What exactly is `BOOST_MSM_NONSTANDALONE_TEST` used for? It doesn't seem to be required for the B2 based build so why for the CMake build?  
>   
> The test Jamfile creates an executable for each source file, in this case BOOST_TEST_MODULE is not defined and a main function is provided in each source file by defining `BOOST_TEST_MODULE`. For CMake I have added a separate `main.cpp` and defined `BOOST_MSM_NONSTANDALONE_TEST`, since all tests run in one executable.  
  
I see, yes that makes sense. You could do the same in the Jamfile if you want, but I guess this separation gives a bit faster turnaround times on CI due to parallelisation.  
  
BTW: For your `file(GLOB ...`) you can add [ `CONFIGURE_DEPENDS` ](https://cmake.org/cmake/help/latest/command/file.html#glob) if you require CMake 3.12+ in the [main CML](https://github.com/boostorg/msm/blob/develop/CMakeLists.txt#L6)  
  
> > Also supporting 3 types of testing (CMake super-project, standalone, b2) might be a maintenance burden. But that's up to you of course.  
>   
> Indeed, I would like to retain the CMake version due to better IDE integration in VS Code. I'll check on that.  
  
I wasn't suggesting dropping the CMake version just to use the regular super-project version that doesn't require a find for an installed Boost. But as mentioned, now the additional code is minimal and if this is just a "hidden feature" you don't need to care about it not working with any Boost version

---

## Comment 4

> Username: chandryan  
> Created_at: 2025-09-18 18:52:04 UTC  
> Url: https://github.com/boostorg/msm/pull/107#issuecomment-3309135152  

> BTW: For your `file(GLOB ...`) you can add [ `CONFIGURE_DEPENDS` ](https://cmake.org/cmake/help/latest/command/file.html#glob) if you require CMake 3.12+ in the [main CML](https://github.com/boostorg/msm/blob/develop/CMakeLists.txt#L6)  
  
Ahh nice, thanks for the tip! I didn't know about this setting yet and usually sticked to listing the files manually.  
   
> > > Also supporting 3 types of testing (CMake super-project, standalone, b2) might be a maintenance burden. But that's up to you of course.  
> >   
> >   
> > Indeed, I would like to retain the CMake version due to better IDE integration in VS Code. I'll check on that.  
>   
> I wasn't suggesting dropping the CMake version just to use the regular super-project version that doesn't require a find for an installed Boost. But as mentioned, now the additional code is minimal and if this is just a "hidden feature" you don't need to care about it not working with any Boost version  
  
I meant to say I would like to retain the CMake version(s) and eventually drop b2, if possible.

---

## Comment 5

> Username: Flamefire  
> Created_at: 2025-09-19 07:20:55 UTC  
> Url: https://github.com/boostorg/msm/pull/107#issuecomment-3310963155  

> Ahh nice, thanks for the tip! I didn't know about this setting yet and usually sticked to listing the files manually.  
  
Listing the files manually is actually preferred because the GLOB does not regenerate the build files when adding new files so manual work is required either way, even if just rerunning CMake. `CONFIGURE_DEPENDS` is a "good enough" alternative though.  
  
> I meant to say I would like to retain the CMake version(s) and eventually drop b2, if possible.  
  
As b2 is still the main build system I wouldn't do so. You can generate your CMake tests automatically from the test Jamfile, although that results in the same individual executables as b2 creates. This ensures they stay consistent.     
And b2 still has advantages like the multi-config testing. With Boost.CI it is easy to get a very wide coverage of compilers and standards with a [small CI file](https://github.com/boostorg/boost-ci/blob/master/.github/workflows/ci.yml). That is based on B2 too although they are CMake tests included but only for a single compiler (per OS)

---

## Review 6 [Commented]

> Username: pdimov  
> Created_at: 2025-09-20 08:11:49 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/msm/pull/107#pullrequestreview-3248871442  

📁 CMakeLists.txt

```diff
  34 |-     Boost::typeof
  35 |- )
  15 |+ if(BOOST_SUPERPROJECT_SOURCE_DIR)
```

> Username: pdimov  
> Created_at: 2025-09-20 08:11:48 UTC  
> Updated_at: 2025-09-20 08:11:49 UTC  
> Url: https://github.com/boostorg/msm/pull/107#discussion_r2365426810  

This check is not correct. Boost libraries can also be used without the superproject, with add_subdirectory or FetchContent.

> Username: chandryan  
> Created_at: 2025-09-20 10:00:49 UTC  
> Updated_at: 2025-09-20 10:04:08 UTC  
> Url: https://github.com/boostorg/msm/pull/107#discussion_r2365564538  

This check is supposed to support the same:  
  
If MSM is used outside of the superproject (`BOOST_SUPERPROJECT_SOURCE_DIR` not present), the else branch below ensures that a `find_package` for Boost is called if it hasn't been called yet.  
  
Then it links against `Boost::boost` instead of the single components, mainly because CMake couldn't find the single components on my machine.  
  
Are they supposed to be available after the call to `find_package`, with identical names as in the superproject build? When I created this file initially I didn't install Boost properly, maybe that was a leftover issue from my side.

> Username: pdimov  
> Created_at: 2025-09-20 13:50:38 UTC  
> Url: https://github.com/boostorg/msm/pull/107#discussion_r2365637184  

That's not the right check. You can use Boost libraries without a superproject, by manually calling add_subdirectory (or FetchContent) on them. In this case there's no superproject, but individual libraries aren't supposed to issue `find_package` calls (because they will find some random system-installed Boost.)  
  
For an example of such use, see e.g. https://github.com/boostorg/uuid/blob/develop/test/cmake_subdir_test/CMakeLists.txt  
  
If you add such a CI job for msm, it will fail with the change above.

> Username: chandryan  
> Created_at: 2025-09-27 07:08:53 UTC  
> Url: https://github.com/boostorg/msm/pull/107#discussion_r2383953700  

I have changed the check to test whether CMake is invoked from the msm directory directly, and only issue `find_package` calls if that is the case.  
  
Could you check whether this is correct now?

> Username: Flamefire  
> Created_at: 2025-09-27 12:51:43 UTC  
> Url: https://github.com/boostorg/msm/pull/107#discussion_r2384127592  

LGTM

> Username: pdimov  
> Created_at: 2025-09-27 13:24:25 UTC  
> Url: https://github.com/boostorg/msm/pull/107#discussion_r2384166528  

Looks good to me as well.

> Username: chandryan  
> Created_at: 2025-09-27 15:52:27 UTC  
> Url: https://github.com/boostorg/msm/pull/107#discussion_r2384223878  

Thanks for your feedback both, resolving this discussion.


---

## Review 7 [Commented]

> Username: pdimov  
> Created_at: 2025-09-20 08:14:08 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/msm/pull/107#pullrequestreview-3248871887  

📁 test/CMakeLists.txt

```diff
  21 |-     target_link_libraries(boost_msm_tests Boost::msm)
  12 |+ target_link_libraries(boost_msm_tests Boost::msm)
  13 |+ if(BOOST_SUPERPROJECT_SOURCE_DIR)
```

> Username: pdimov  
> Created_at: 2025-09-20 08:14:08 UTC  
> Url: https://github.com/boostorg/msm/pull/107#discussion_r2365427276  

Not sure what the point of this is either.

> Username: chandryan  
> Created_at: 2025-09-20 10:02:31 UTC  
> Updated_at: 2025-09-20 10:04:08 UTC  
> Url: https://github.com/boostorg/msm/pull/107#discussion_r2365564940  

Similar reason as above:  
  
I assumed the target `Boost::unit_test_framework` is not available when using `find_package` in a standalone build, so I wrapped the linkage in this check.

> Username: Flamefire  
> Created_at: 2025-09-20 11:00:44 UTC  
> Url: https://github.com/boostorg/msm/pull/107#discussion_r2365584802  

You can just add `unit_test_framework` to the COMPONENTS above

> Username: chandryan  
> Created_at: 2025-09-27 06:57:47 UTC  
> Updated_at: 2025-09-27 07:07:45 UTC  
> Url: https://github.com/boostorg/msm/pull/107#discussion_r2383950547  

There is one thing that is odd for me:  
I can add `unit_test_framework` in the `COMPONENTS`, but it doesn't work with others such as `any`. Even when I mention `any` in b2's bootstrapping by adding it to the `--with-libraries` parameter, it cannot be found as a component in the `find_package` call.  
  
I assume this is because `any` is a header-only library and gets treated differently in b2's installation process, and that's why I have to use a different `target_link_libraries` command when building MSM standalone.  
  
Is this the intended behavior or am I still doing something wrong?

> Username: Flamefire  
> Created_at: 2025-09-27 12:57:01 UTC  
> Url: https://github.com/boostorg/msm/pull/107#discussion_r2384144479  

> I assume this is because any is a header-only library and gets treated differently in b2's installation process,  
  
This is the case yes. However it isn't b2 itself but the build file of the library. Compare https://github.com/boostorg/any/blob/9b1941c787f2769fcb6afdb0cef621db80f953a6/build.jam#L24-L25 with https://github.com/boostorg/locale/blob/3466ac7525b1607477fb4585d4ead8cb1e37d124/build.jam#L36-L38  
  
Header-only libraries have no `install` call there. @pdimov I remember a recent discussion about that in Slack but forgot the result. Was it really impossible (in general or currently) to "install" header-only libraries with that to generate the CMake configs or is that just an omission?

> Username: pdimov  
> Created_at: 2025-09-27 13:22:33 UTC  
> Url: https://github.com/boostorg/msm/pull/107#discussion_r2384165637  

Neither the original FindBoost nor its replacement configs have ever had targets for header-only libraries.  
  
As for build.jam, these didn't exist until very recently.  
  
I don't know whether now, when they do exist, it's going to be possible to "install" header-only libraries as well. But even if it is, if your `find_package(Boost)` call finds an earlier version of Boost, it's not going to have the targets.


---

## Comment 8

> Username: pdimov  
> Created_at: 2025-09-28 07:39:29 UTC  
> Url: https://github.com/boostorg/msm/pull/107#issuecomment-3342478776  

Now the CMake tests fail with  
```  
In file included from /home/runner/work/boost/boost/libs/msm/test/Back11OrthogonalDeferred3WithPuml.cpp:15:  
/home/runner/work/boost/boost/libs/msm/include/boost/msm/front/puml/puml.hpp: In function ‘constexpr auto boost::msm::front::puml::detail::parse_guard_simple(Func)’:  
/home/runner/work/boost/boost/libs/msm/include/boost/msm/front/puml/puml.hpp:507:25: error: lambda-expression in unevaluated context only available with ‘-std=c++20’ or ‘-std=gnu++20’  
  507 |                         [=]() {return boost::msm::front::puml::detail::cleanup_token(guard_func().substr(0, last_or_pos)); })),  
      |                         ^  
```  
https://github.com/boostorg/boost/actions/runs/18063746716/job/51403678027

---

## Comment 9

> Username: Flamefire  
> Created_at: 2025-09-28 11:30:09 UTC  
> Url: https://github.com/boostorg/msm/pull/107#issuecomment-3342994890  

`if(CMAKE_CXX_STANDARD LESS 20)` succeeds if `CMAKE_CXX_STANDARD` isn't set as seeming `"CMAKE_CXX_STANDARD" < 20` (i.e. the string) doesn't hold  
  
https://github.com/boostorg/msm/pull/116 should fix it.

---

## Comment 10

> Username: chandryan  
> Created_at: 2025-09-28 12:16:02 UTC  
> Url: https://github.com/boostorg/msm/pull/107#issuecomment-3343106395  

> Now the CMake tests fail with  
>   
> ```  
> In file included from /home/runner/work/boost/boost/libs/msm/test/Back11OrthogonalDeferred3WithPuml.cpp:15:  
> /home/runner/work/boost/boost/libs/msm/include/boost/msm/front/puml/puml.hpp: In function ‘constexpr auto boost::msm::front::puml::detail::parse_guard_simple(Func)’:  
> /home/runner/work/boost/boost/libs/msm/include/boost/msm/front/puml/puml.hpp:507:25: error: lambda-expression in unevaluated context only available with ‘-std=c++20’ or ‘-std=gnu++20’  
>   507 |                         [=]() {return boost::msm::front::puml::detail::cleanup_token(guard_func().substr(0, last_or_pos)); })),  
>       |                         ^  
> ```  
>   
> https://github.com/boostorg/boost/actions/runs/18063746716/job/51403678027  
  
Do you think it makes sense to add such a job to the MSM repo for better regression?

---

## Comment 11

> Username: Flamefire  
> Created_at: 2025-09-28 12:41:18 UTC  
> Url: https://github.com/boostorg/msm/pull/107#issuecomment-3343180386  

The CI is indeed missing CMake tests. Either add them or switch to the reusable Boost.CI workflow. See https://github.com/boostorg/boost-ci/blob/master/.github/workflows/ci.yml for big coverage of configs and automatic updates/fixes

---
