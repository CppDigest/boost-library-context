# #106 - backmp11: Remove unneeded header inclusions [Closed]

> Username: chandryan  
> Created at: 2025-09-16 05:36:45 UTC  
> Updated at: 2026-01-27 21:20:10 UTC  
> Closed at: 2026-01-27 21:20:09 UTC  
> Url: https://github.com/boostorg/msm/issues/106  

**Description**  
  
The backend `backmp11` successfully resolved long compilation times of big state machines by migrating compile-time intensive metaprogramming usages from MPL to Mp11 (see https://github.com/chandryan/fsm-benchmarks).  
  
There is more optimization potential considering the static parts of the compilation time: Preprocessing the included headers takes roughly 2 seconds. From a compile time trace the following candidates look promising to further reduce the compilation time:  
  
- [ ] ~fusion~ is used a lot in the frontend, requires more discussion  
- [x] bind has only 3 occurrences left  
- [x] proto can be removed when cutting off eUML compatibility  
- [x] make boost::serialization inclusion opt-in  
- [x] mpl_graph is also worth a shot

---

## Comment 1

> Username: chandryan  
> Created at: 2026-01-27 21:20:10 UTC  
> Url: https://github.com/boostorg/msm/issues/106#issuecomment-3807590991  

All unneeded headers have been removed in backmp11. Header inclusion now takes about 0.7 sec, with the majority of processing time spent in STL headers.
