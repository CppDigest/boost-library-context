# #808 Refine CI for C++23 era [Merged]

> Username: saki7  
> Created at: 2025-09-04 16:20:11 UTC  
> Updated at: 2025-09-05 04:03:17 UTC  
> Merged at: 2025-09-05 04:03:13 UTC  
> Closed at: 2025-09-05 04:03:13 UTC  
> Url: https://github.com/boostorg/spirit/pull/808  

## Goals  
  
- [x] Drop AppVeyor, Drone, Travis (They are leftovers for supporting Windows in 10 years ago)  
- [x] Implement Windows support in GHA  
- [x] Make workflow names human-friendly  
- [x] Make everything fast  
  - [x] Path-based update check on subdirectory level  
  - [x] Remove obsolete travis-related thingy in GHA scripts  
  - [x] Drop all unnecessary .cpp builds on dependent libraries  
  - [x] Cache all artifacts (including header-only ones) properly on GHA

---
