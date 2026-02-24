# #111 - CMake tests need to be made dependencies on the `tests` target [Closed]

> Username: pdimov  
> Created at: 2025-09-19 14:02:49 UTC  
> Updated at: 2025-09-27 19:01:01 UTC  
> Closed at: 2025-09-27 19:01:01 UTC  
> Url: https://github.com/boostorg/msm/issues/111  

Boost's testing procedure when using CMake is documented here  
  
https://github.com/boostorg/cmake#testing-boost-with-cmake  
  
and it consists of building the tests with  
```  
cmake --build . --target tests  
```  
  
For this to work, the tests need to be made dependencies of the `tests` custom target. BoostTest.cmake does this automatically, but since you aren't using it, you need to do this manually.  
  
```  
if(NOT TARGET tests)  
  add_custom_target(tests)  
endif()  
  
add_dependencies(tests boost_msm_tests)  
```  
  
Otherwise, testing with the above procedure fails, e.g. https://github.com/boostorg/boost/actions/runs/17858710252/job/50783716320

---

## Comment 1

> Username: chandryan  
> Created at: 2025-09-20 07:07:19 UTC  
> Updated at: 2025-09-20 07:26:06 UTC  
> Url: https://github.com/boostorg/msm/issues/111#issuecomment-3314685208  

Thanks for the hint! I have pushed the required change in https://github.com/boostorg/msm/pull/107.  
After the discussion in https://github.com/boostorg/msm/pull/107 I'd like to take a look at using BoostTestJamfile.cmake as alternative, though I'll be mostly unavailable for the next ~2 weeks.  
  
Should this fix be merged ugently to unblock something in the Boost CI or can it wait until I check out BoostTestJamfile.cmake?

---

## Comment 2

> Username: pdimov  
> Created at: 2025-09-20 08:15:04 UTC  
> Url: https://github.com/boostorg/msm/issues/111#issuecomment-3314760681  

#107 doesn't look good to me. I'd suggest only applying the changes above, separately.
