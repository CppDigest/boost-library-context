# #99 Remove find_package(boost_serialization) [Closed]

> Username: Flamefire  
> Created at: 2025-09-11 11:02:31 UTC  
> Updated at: 2025-09-16 07:01:40 UTC  
> Closed at: 2025-09-16 07:01:36 UTC  
> Url: https://github.com/boostorg/msm/pull/99  

Not required as this is part of the super-project build which adds this automatically.  
  
This leads to either failures when Boost isn't installed yet or potentially wrong Boost installations found when Building Boost tests  
  
So just remove it.

---

## Comment 1

> Username: chandryan  
> Created_at: 2025-09-14 13:12:58 UTC  
> Url: https://github.com/boostorg/msm/pull/99#issuecomment-3289535090  

I recently added this file, though for now it was only meant for internal testing outside of the super-project build.  
  
According to [the readme I found through your PR](https://github.com/boostorg/cmake/blob/develop/developer.md#testing-support) I suppose it needs to be changed to the following, is that correct?  
  
```  
if(BUILD_TESTING AND CMAKE_SOURCE_DIR STREQUAL CMAKE_CURRENT_SOURCE_DIR)  
  
  add_subdirectory(test)  
  
endif()  
```

---

## Comment 2

> Username: Flamefire  
> Created_at: 2025-09-14 18:00:08 UTC  
> Url: https://github.com/boostorg/msm/pull/99#issuecomment-3289727375  

> According to [the readme I found through your PR](https://github.com/boostorg/cmake/blob/develop/developer.md#testing-support) I suppose it needs to be changed to the following, is that correct?  
  
Yes something like that. Although I'd suggest to follow the common approach and use the Boost super-project as MSM has dependencies on a couple other Boost libraries besides serialization you'd need too. Any specific reason you don't do that yet?  
  
You could go with a split approach in the test CML. Something like:  
  
```  
if(CMAKE_SOURCE_DIR STREQUAL CMAKE_CURRENT_SOURCE_DIR)  
  # Build test folder directly  
  target_include_directories(boost_msm_tests PRIVATE ../include)  
  # Includes other Boost header-only libraries  
  find_package(Boost COMPONENTS serialization REQUIRED)  
  target_link_libraries(boost_msm_tests Boost::serialization)  
else()  
  # Part of super project or called from parent CML  
  target_link_libraries(boost_msm_tests Boost::msm)  
endif()  
```  
  
This way `cmake msm/test` works as currently and the tests would also be part of the super-project build (if they are ready for that)

---

## Comment 3

> Username: chandryan  
> Created_at: 2025-09-15 20:08:20 UTC  
> Url: https://github.com/boostorg/msm/pull/99#issuecomment-3293740386  

> Yes something like that. Although I'd suggest to follow the common approach and use the Boost super-project as MSM has dependencies on a couple other Boost libraries besides serialization you'd need too. Any specific reason you don't do that yet?  
  
No specific reason, I should give it a try soon :)  
  
> You could go with a split approach in the test CML. Something like: [...]  
  
Thanks for the recommendation, that looks like a good approach 👍

---

## Comment 4

> Username: chandryan  
> Created_at: 2025-09-16 04:42:26 UTC  
> Url: https://github.com/boostorg/msm/pull/99#issuecomment-3295114829  

Merged your suggestion in https://github.com/boostorg/msm/pull/103

---
