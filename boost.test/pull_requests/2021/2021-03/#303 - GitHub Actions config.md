# #303 GitHub Actions config [Closed]

> Username: sdarwin  
> Created at: 2021-03-04 18:23:38 UTC  
> Updated at: 2023-01-31 16:06:13 UTC  
> Closed at: 2023-01-31 16:06:12 UTC  
> Url: https://github.com/boostorg/test/pull/303  

GitHub Actions CI script, generated from the .travis.yml file. Please refer to https://github.com/CPPAlliance/githubactions for more information and instructions.

---

## Comment 1

> Username: raffienficiaud  
> Created_at: 2022-03-05 15:59:18 UTC  
> Url: https://github.com/boostorg/test/pull/303#issuecomment-1059787619  

Thanks for this, however it fails badly on almost all tasks. Fixing this would take some time.

---

## Comment 2

> Username: sdarwin  
> Created_at: 2022-03-05 16:40:34 UTC  
> Url: https://github.com/boostorg/test/pull/303#issuecomment-1059793941  

Ubuntu-16.04 is not supported any longer.  I will send an update to this pull request next week.  Or, you may copy a ci.yml from https://github.com/boostorg/boost-ci/blob/master/.github/workflows/ci.yml

---

## Comment 3

> Username: raffienficiaud  
> Created_at: 2022-03-05 17:43:00 UTC  
> Url: https://github.com/boostorg/test/pull/303#issuecomment-1059803585  

> Ubuntu-16.04 is not supported any longer.  
  
I noticed and updated to 20.04, that is not the issue though. I would also favor bare/native/vanilla commands rather than relying on an external build repository. I find it very hard to follow/maintain.

---

## Comment 4

> Username: raffienficiaud  
> Created_at: 2022-03-05 21:25:14 UTC  
> Url: https://github.com/boostorg/test/pull/303#issuecomment-1059833388  

See #332 for the results of the GH actions

---

## Comment 5

> Username: sdarwin  
> Created_at: 2022-03-08 00:19:20 UTC  
> Url: https://github.com/boostorg/test/pull/303#issuecomment-1061275104  

Just committed a fix to this pull request.   
Results are at https://github.com/sdarwin/test/actions/runs/1948681877    
There are ongoing issues here, boost-ci might have a recently added bug, I will ask about it tomorrow.

---

## Comment 6

> Username: sdarwin  
> Created_at: 2022-03-10 17:12:01 UTC  
> Url: https://github.com/boostorg/test/pull/303#issuecomment-1064300343  

Hi @raffienficiaud everything should be working now.  The latest run of this config on Github Actions is completely successful (except for codecov, because the codecov token must be added to the configuration.).   
 https://github.com/sdarwin/test/actions/runs/1964203849

---

## Comment 7

> Username: Flamefire  
> Created_at: 2022-10-07 10:47:46 UTC  
> Url: https://github.com/boostorg/test/pull/303#issuecomment-1271431315  

I would suggest to use the common template from https://github.com/boostorg/boost-ci/

---

## Comment 8

> Username: raffienficiaud  
> Created_at: 2022-10-07 10:49:50 UTC  
> Url: https://github.com/boostorg/test/pull/303#issuecomment-1271433304  

@Flamefire this was the case for several years, but neither Travis nor Appveyor were able to run the tests until the end, it was extremely slow to get anything from those CI systems, and there were a lot of false negatives. I removed that I believe beginning of this year.

---

## Comment 9

> Username: Flamefire  
> Created_at: 2022-10-07 10:58:11 UTC  
> Url: https://github.com/boostorg/test/pull/303#issuecomment-1271441705  

Travis doesn't run anymore anyway for OSS projects (to limitted time) hence we developed a Github Actions config. Take a look please. I'm currently testing this in a fork: https://github.com/Flamefire/test/actions

---

## Comment 10

> Username: Flamefire  
> Created_at: 2022-10-07 14:26:56 UTC  
> Updated_at: 2022-10-07 14:28:41 UTC  
> Url: https://github.com/boostorg/test/pull/303#issuecomment-1271669513  

I would strongly suggest to get some CI in place. Please check my branch https://github.com/Flamefire/test/tree/feature/ci for the first couple of changes, enough to get something running.  
  
Currently the B2 test suite fails for a couple tests and the CMake test suite doesn't seem to work at all  
  
Note that the [workflow file](https://github.com/Flamefire/test/blob/feature/ci/.github/workflows/ci.yml) is well structured so you can select which configurations you can test easily. It also makes use of ccache for most jobs which should speed up subsequent runs. I'd suggest to trim the number of jobs and combinations (e.g. C++ standard levels) down as some builds take around an hour, which IMO is too much.  
  
> and there were a lot of false negatives.   
  
What kind of false negatives?  
  
Currently running the test suite via b2 yields e.g. on my machine:  
  
```  
libs/test/example/unit_test_example_16.cpp:33:51: error: invalid conversion from ‘bool (*)()’ to ‘boost::unit_test::init_unit_test_func’ {aka ‘boost::unit_test::test_suite* (*)(int, char**)’} [-fpermissive]  
   33 |   int retCode = boost::unit_test::unit_test_main( &init_unit_test, argc, argv );  
      |                                                   ^~~~~~~~~~~~~~~  
      |                                                   |  
      |                                                   bool (*)()  
```  
  
And on [CI](https://github.com/Flamefire/test/actions/runs/3204990933/jobs/5236955770)   
  
```  
bin.v2/libs/test/example/external_main_example_1.test/clang-linux-3.5/release/cxxstd-11-iso/link-static/threading-multi/visibility-hidden/external_main_example_1.o: In function `main':  
/github/home/.ccache/tmp/external_m.stdout.73984a297aea.43523.2TKKWV.ii:(.text+0x4f0): undefined reference to `boost::unit_test::unit_test_main(bool (*)(), int, char**)'  
clang: error: linker command failed with exit code 1 (use -v to see invocation)  
```  
  
Those look like valid failures pointing to issues (in this case likely with the tests)  
  
And e.g. to handle #357 it would be good to add CI with a test that detects this and then fix it.

---

## Comment 11

> Username: raffienficiaud  
> Created_at: 2022-10-07 15:21:06 UTC  
> Url: https://github.com/boostorg/test/pull/303#issuecomment-1271734840  

There is a CI in place, I am using it when I need to work on boost.test. It is not handy as handy as GH actions, this is why I wanted to go for the GH action solution.  See [here](https://nan.yayimorphology.org/bamboo/browse/BT-BTCB7). There is also the official test matrix.  
  
The `build/CmakeLists.txt` was for my own development, back in the times when boost was not using cmake at all. Now things have changed, but I am not covering the CMakeLists.txt on the root of boost.test with any test.  
  
About the false positives, flagging a build red while it is green, mostly because of many timeouts, but also some weird errors that I could not reproduce on any platform (including the official boost test matrix).   
  
Concerning the issue you are mentioning for the file `libs/test/example/unit_test_example_16.cpp`, we have this `typedef` in effect:  
  
```  
/// Different depending on whether BOOST_TEST_ALTERNATIVE_INIT_API is defined or not  
#ifdef BOOST_TEST_ALTERNATIVE_INIT_API  
typedef bool        (*init_unit_test_func)();  
#else  
typedef test_suite* (*init_unit_test_func)( int, char* [] );  
#endif  
```  
  
which depends on `BOOST_TEST_ALTERNATIVE_INIT_API` which in turn depends on `BOOST_TEST_DYN_LINK`. Dynamic linking used to be the default here, so the `typedef` is correct. So it seems that this may be a CI issue.

---

## Comment 12

> Username: raffienficiaud  
> Created_at: 2022-10-07 15:26:20 UTC  
> Url: https://github.com/boostorg/test/pull/303#issuecomment-1271740261  

> down as some builds take around an hour, which IMO is too much.  
  
This takes time yes, and this is why most of the CI solutions cloud based will not work long term or as is (GH actions have limited build time / month if I recall). This means that we need to have at least 2 solutions in place: one giving fast feedback on the cloud, and one that does a good coverage on many platforms.

---

## Comment 13

> Username: Flamefire  
> Created_at: 2022-10-08 11:42:18 UTC  
> Url: https://github.com/boostorg/test/pull/303#issuecomment-1272299894  

> There is a CI in place, I am using it when I need to work on boost.test. It is not handy as handy as GH actions, this is why I wanted to go for the GH action solution. See [here](https://nan.yayimorphology.org/bamboo/browse/BT-BTCB7). There is also the official test matrix.  
  
That link gives me "Server not found" errors.  
  
> Dynamic linking used to be the default here, so the typedef is correct. So it seems that this may be a CI issue.  
  
This happens locally on my Ubuntu machine when running `b2 libs/test/test` on a boost checkout, so it rather looks like the test-Jamfile got outdated and `unit_test_example_16` is being built against the wrong linkage of Boost.Test, and/or it should conditionally define/not define `BOOST_TEST_ALTERNATIVE_INIT_API`  
  
> The build/CmakeLists.txt was for my own development, back in the times when boost was not using cmake at all. Now things have changed, but I am not covering the CMakeLists.txt on the root of boost.test with any test.  
  
I could offer help removing the build/CML in favor of a root CML with tests included as I already started an attempt (see previous message)  
  
>  [...] this is why I wanted to go for the GH action solution [...] This means that we need to have at least 2 solutions in place: one giving fast feedback on the cloud, and one that does a good coverage on many platforms.  
  
The solution I propose is a GH action solution. The template is on Boost.CI and has large coverage of many configurations and is structured so that it is easy to select which ones to test keeping the logic part untouched. It has many issues already solved due to combined effort of a couple devs creating scripts for the common logic. And especially ccache comes in handy for reducing build times.     
Again I can offer help in integrating this as I already started, but it is up to you which configurations should be mainly tested. I could use the ~24 configurations you started in #332 for example and PR the finished config.  
  
As for the 2nd solution: Some have started to provide CI services on a CircleCI instance hosted IIRC by CppAlliance (maybe @sdarwin knows more). CI script templates and description how to integrate it into any boost lib are also in the Boost.CI repo.

---

## Comment 14

> Username: mborland  
> Created_at: 2023-01-31 16:06:12 UTC  
> Url: https://github.com/boostorg/test/pull/303#issuecomment-1410654580  

Superseded by #370

---
