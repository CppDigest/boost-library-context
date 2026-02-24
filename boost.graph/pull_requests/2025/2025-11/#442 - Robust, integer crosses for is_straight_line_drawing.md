# #442 Robust, integer crosses for is_straight_line_drawing [Merged]

> Username: tinko92  
> Created at: 2025-11-28 15:53:48 UTC  
> Updated at: 2025-12-01 08:23:31 UTC  
> Merged at: 2025-11-30 22:18:39 UTC  
> Closed at: 2025-11-30 22:18:39 UTC  
> Url: https://github.com/boostorg/graph/pull/442  

This PR, based on what I propose in https://github.com/boostorg/geometry/issues/1434, drops the use of Boost.Geometry in is_straight_line_drawing.hpp in favour of a simple, int128-based test. This drops the dependency on Boost.Geometry and adds a dependency on Boost.Multiprecision (which would otherwise come transitively through BG):  
  
This new test makes the assumption that coordinate values are integers.  
  
It has the following semantics (If I didn't miss something) based on my understanding of the definition of planar drawing (non-parallel edges meet only at shared endpoints):  
- An X-like crossing is a violation (same as before)  
- A T-like crossing is a violation (no crossing in Boost.Geometry semantics)  
- A partial overlap is a violation (no crossing in Boost.Geometry semantics)  
- A complete overlap (parallel edge) is not a violation (same as before)  
- Meeting only at one end point is not a violation (same as before)  
  
If any change to this is desired, I can edit.  
  
It should have the following robustness properties:  
- If coordinate values lie in some integer interval not longer than 2^63, results should be correct. Boost.Geometry calculates exactly after conversion to doubles, but initial conversion to doubles may lose precision from 64-bit ints (more of a theoretical concern though, as it only happens above ~2^53).  
  
Performance properties:  
- I do not know how performance-critical this is and did not run benchmarks. If int128_t is an efficient compiler built-in, orientation2d may be (I expect) ~1-2x as expensive as side_robust. If this is a concern, I can edit the PR to add a classical double-based, ~6 LOC filter (if incorrectness for values above ~2^53 is no concern) or a not-so-classical double-based filter (that is more careful about large integers, slightly slower).  
  
Tests run successfully (except isomorphism which also fails on develop for me due to cannot open file) on my device but its CPU is non-x86.

---

## Comment 1

> Username: jeremy-murphy  
> Created_at: 2025-11-29 11:56:42 UTC  
> Url: https://github.com/boostorg/graph/pull/442#issuecomment-3591608025  

After all, this is probably the best way to go. Thank you so much for the thorough analysis and implementation.   
One thing, if this is adding a new dependency on Multiprecision, doesn't it need to go into the b2 and CMake build files?

---

## Comment 2

> Username: tinko92  
> Created_at: 2025-11-30 05:39:47 UTC  
> Url: https://github.com/boostorg/graph/pull/442#issuecomment-3592232442  

Ah, I forgot. Added the dependency and squashed it into one commit.  
  
Since I am not so familiar with the boost build system and its cmake setup, the following two completed without error on my machine from the boost top-level directory after adding the dependency (for testing):  
```sh  
./b2 --with-graph -j8  
cmake -S . -B build -DBOOST_INCLUDE_LIBRARIES=graph && cmake --build build  
```

---
