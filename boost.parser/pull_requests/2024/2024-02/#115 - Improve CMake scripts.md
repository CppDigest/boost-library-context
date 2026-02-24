# #115 Improve CMake scripts [Closed]

> Username: Flamefire  
> Created at: 2024-02-24 17:14:21 UTC  
> Updated at: 2024-02-27 09:19:59 UTC  
> Closed at: 2024-02-27 02:58:14 UTC  
> Url: https://github.com/boostorg/parser/pull/115  

I took a shot at improving the CMake scripts.  
  
- `include_directories`, `add_definitions` etc replaced by their target specific variants  
- replace the non-standard `CXX_STD` variable by mostly the `target_compile_features` which avoids all the repetition  
- In CI scripts `CMAKE_CXX_STANDARD` replaces `CXX_STD` to the same effect  
- Some more simplifications and random fixes (see commits)  
  
I also removed the unused GoogleBenchmark moving the dependency search and definition into the main CML, making them easier to find. I'd suggest switching to Boost.Test though given you use Boost already which would avoid the vendored dependency

---

## Comment 1

> Username: tzlaine  
> Created_at: 2024-02-27 02:58:14 UTC  
> Url: https://github.com/boostorg/parser/pull/115#issuecomment-1965698650  

Thanks for the offer, but I won't merge this PR.  This is a header-only library, and as such the build is basically my personal playground.  It doesn't really matter if I take shortcuts like unconditionally adding -g.  
  
Some of these are not actually equivalent to the previous CMake behavior in ways that are harmful to the way I work, or that don't work at all (see the failed builds above).  
  
CMake: Consolidate *_DIR usages - This eliminates the cmake/ dir, and dependencies.cmake.  I have this organization in every Github project.  I don't want to change it, beacause familiarity is important in finding things.  
  
CMake: Improve check target - This is simply not how I want my tests to run.  I've done it like that in the past, but prefer the way I do it now -- especially the -j4!  
  
CMake: Avoid repetition in test definitions - You actually broke one of the tests when you made this change (the underscore at the end of parser_ is significant).  You also changed the naming pattern of the tests, which is going to make it harder for me to run them in future.  
  
Use CMAKE_CXX_STANDARD & feature instead of custom variable - This only works if you stay current on CMake, which I do not, and don't use C++ features before the compiler versions they live in make their way into CMake, which I do.  I do this CXX_STD thing because I *very frequently* need a `-std=C++XX` that CMake claims does not exist, and yet totally exists.

---

## Comment 2

> Username: Flamefire  
> Created_at: 2024-02-27 09:19:55 UTC  
> Url: https://github.com/boostorg/parser/pull/115#issuecomment-1966112448  

> Thanks for the offer, but I won't merge this PR. This is a header-only library, and as such the build is basically my personal playground. It doesn't really matter if I take shortcuts like unconditionally adding -g.  
  
Well even though it is header-only having a build system for the tests and examples is helpful especially for someone wanting to try it out. But I understand your motivation and updated the branch according to your comments. Check if you might want to take some or all of those changes (I think you need to reopen the PR such that changes are reflected here)  
  
> or that don't work at all (see the failed builds above).  
  
Yes that was a mistake on my side using the wrong keyword. Fixed easily  
  
> CMake: Consolidate *_DIR usages - This eliminates the cmake/ dir, and dependencies.cmake. I have this organization in every Github project. I don't want to change it, because familiarity is important in finding things.  
  
I readded that file and just used the changes such that the correct `_DIR` variables are used. `CMAKE_HOME_DIRECTORY` is actively discouraged from use and `PROJECT_SOURCE_DIR` is safer than `CMAKE_SOURCE_DIR`. `CMAKE_CURRENT_SOURCE_DIR` could be used with good reasoning though.  
  
> CMake: Improve check target - This is simply not how I want my tests to run. I've done it like that in the past, but prefer the way I do it now -- especially the -j4!  
  
Makes sense. Then just replacing `CMAKE_CFG_INTDIR` by `$<CONFIG>` is more compatible, kept that part for now  
  
> CMake: Avoid repetition in test definitions - You actually broke one of the tests when you made this change (the underscore at the end of parser_ is significant). You also changed the naming pattern of the tests, which is going to make it harder for me to run them in future.  
  
I didn't broke the test due to exactly that change in the naming pattern. And `parser_api` was clearly a duplication wasn't it? I enhanced that commit to use the current naming pattern while still avoiding the repetition. See if you like that  
  
> Use CMAKE_CXX_STANDARD & feature instead of custom variable - This only works if you stay current on CMake, which I do not, and don't use C++ features before the compiler versions they live in make their way into CMake, which I do. I do this CXX_STD thing because I very frequently need a -std=C++XX that CMake claims does not exist, and yet totally exists.  
  
The `cxx_std_nn` feature should be pretty well supported. I expect this to be at least as well supported as the `CXX_STANDARD` property.      
Anyway I changed the commit to just rename `CXX_STD` to `CMAKE_CXX_STANDARD` which avoids having to set the property for each target as that is what that variable does and it is easier for others to discover due to being standard. How about that?  
  
  
Feel free to merge the branch or just cherry-pick commits you like. Just wanted to offer some improvements and with the explanations now it should make more sense.

---
