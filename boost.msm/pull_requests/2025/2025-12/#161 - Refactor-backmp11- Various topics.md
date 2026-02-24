# #161 Refactor/backmp11: Various topics [Merged]

> Username: chandryan  
> Created at: 2025-12-22 11:02:42 UTC  
> Updated at: 2026-01-02 21:47:31 UTC  
> Merged at: 2026-01-02 21:47:21 UTC  
> Closed at: 2026-01-02 21:47:21 UTC  
> Url: https://github.com/boostorg/msm/pull/161  

Applied refactorings:  
  
- Replaced remaining occurrences of `HandledEnum` with `process_result`  
- Added bit operations for `process_result` for better readability  
- Replaced the `process_event` callback "ping-pong" with a `normalize_event` function for better readability  
- Removed the MSVC workaround in the dispatch table generation of the `favor_runtime_speed` policy, as MSVC seems to be able to handle the optimized way after the fix of event deferral was applied  
- refactored remaining MPL usages to Mp11 in favor_runtime_speed  
- refactored state generation to work more directly without adding not real rows  
- first contents specific to transition tables in the backend moved to separate header  
- reduced the amount of eager unneeded template instantiations to reduce mostly RAM usage but also a little bit of compile time  
- improved the visitor implementation to accept multiple predicates, that are processed sequentially (needed to reduce compile time overheads when checking for deferred events)

---
