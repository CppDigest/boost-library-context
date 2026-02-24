# #5 [CMake] Add minimal cmake support [Merged]

> Username: Mike-Devel  
> Created at: 2019-04-18 16:06:28 UTC  
> Updated at: 2019-05-07 20:45:10 UTC  
> Merged at: 2019-05-07 05:37:25 UTC  
> Closed at: 2019-05-07 05:37:25 UTC  
> Url: https://github.com/boostorg/ratio/pull/5  

This PR adds minimal cmake support to boost.ratio, which means:  
  
- It is not meant to be used standalone, but as a sub project (i.e. via `add_subdirectory(<path-to-boost-ratio>)` )  
- It excpects that the dependencies like `Boost::config` are similarly provided by other sub_projects or directly by the super project - it does not look for them by itself.  
- It can only be used to compile and run the `*_pass` unit tests.  
  
Basic test instructions (assuming cmake is already installed and this version of Boost.Ratio is cloned into its usual place inside the boost super-project (i.e. `<boost-root/libs/ratio>`):  
  
- Make sure, recent versions (i.e. from develop) of all libraries Boost.Ratio depends on directly or indirectly are cloned into sibling folders (e.g. just make a recursive clone of the the boost super project)  
- Add a cmake file like the folowing into the root folder of the boost super project: https://github.com/Mike-Devel/boost/blob/min_cmake/CMakeLists.txt  
  It essentially just calls `add_subdirectory` on all `./libs/<library-name>` subfolders that have a CMakeLists.txt file, but excludes a few libraries (if present) that don't work well with the `add_subdirectory` workflow yet.  
  
Then from a command line (e.g. from boost-root):  
  
    - mkdir __build__  
    - cd __build__  
    - cmake -DBOOST_RATIO_INCLUDE_TESTS=On <path-to-boost-root>  
    - cmake --build .  
    - ctest .

---

## Comment 1

> Username: viboes  
> Created_at: 2019-05-05 21:29:06 UTC  
> Url: https://github.com/boostorg/ratio/pull/5#issuecomment-489464985  

Hi, the single problem I see is to know how we can see that this work on a regression test.  
  
How would we maintain it without having a test on the CI that uses it?

---

## Comment 2

> Username: Mike-Devel  
> Created_at: 2019-05-06 07:22:31 UTC  
> Url: https://github.com/boostorg/ratio/pull/5#issuecomment-489522902  

> How would we maintain it without having a test on the CI that uses it?  
  
Then let's just put it into the CI. The only question is where to put the root-cmake script from the PR I mentioned,  such that it can easily be downloaded/copied into boost-root during the CI job. It should be somewhere under your and/or Boost's control.  
  
With some slight modifications,  it could also be placed in a separate `cmake` sub-folder in this repository (either directly in ratio's root dir or e.g. in `test/cmake`

---

## Comment 3

> Username: viboes  
> Created_at: 2019-05-07 05:36:57 UTC  
> Url: https://github.com/boostorg/ratio/pull/5#issuecomment-489915445  

I'm for having a sub-folder cmake that could check this is working.  
I would be also for having Cmake support for the test.   
As Boost doesn't have yet a Cmake CI, it could be run using Travis, Appveyor and/or CircleCI.  
Please be free to propose anything that could address a way to check these files will be not broken as soon as we introduce some breaking changes.

---
