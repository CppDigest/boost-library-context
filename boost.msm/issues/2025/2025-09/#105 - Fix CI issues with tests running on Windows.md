# #105 - Fix CI issues with tests running on Windows [Closed]

> Username: chandryan  
> Created at: 2025-09-16 05:17:01 UTC  
> Updated at: 2025-09-28 15:48:26 UTC  
> Closed at: 2025-09-28 15:48:26 UTC  
> Url: https://github.com/boostorg/msm/issues/105  

**Description**  
  
Two jobs in the CI are still failing:  
  
- Windows gcc, serialization-related: https://github.com/chandryan/msm/actions/runs/17745967181/job/50433123767?pr=10  
- Windows msvc, multiple tests don't seem to compile: https://github.com/boostorg/msm/actions/runs/17832442295/job/50700756487?pr=107

---

## Comment 1

> Username: chandryan  
> Created at: 2025-09-19 05:18:04 UTC  
> Updated at: 2025-09-27 12:50:40 UTC  
> Url: https://github.com/boostorg/msm/issues/105#issuecomment-3310616135  

Got some insights on the compilation failures on MSVC:  
  
- `Back11OrthogonalDeferred3WithPuml.cpp`: MSVC runs out of heap space with a 32-bit compilation. I suppose it's reasonable to limit the test case execution to 64-bit  
- ~`OrthogonalDeferredEuml.cpp`: The compiler crashes with an access violation when trying to compile backmp11/favor_runtime_speed. The combination backmp11/favor_compile_time works, though. Support for eUML in backmp11 was planned to be removed anyway, which should also remove this compilation failure.~  
Found the location and a workaround for the code where MSVC crashes.
