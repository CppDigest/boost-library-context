# #450 Fix v1 tests after v2 became the default [Merged]

> Username: jgreitemann  
> Created at: 2025-01-12 19:50:41 UTC  
> Updated at: 2025-02-21 00:08:37 UTC  
> Merged at: 2025-02-21 00:08:36 UTC  
> Closed at: 2025-02-21 00:08:36 UTC  
> Url: https://github.com/boostorg/process/pull/450  

As of 2ccd97cd48, v2 is the default when using the unversioned includes. While working on #449, I noticed that this broke the v1 tests which were still using them.  
  
I'm not sure what the plan is for the v1 tests. I've seen that they have been disabled in the Jamfile https://github.com/boostorg/process/blob/b529769eb5d08d9f95b0130fd2f1a49acad330f4/test/Jamfile.jam#L6-L7  
so I assume that is intentional? At the same time, the CMake project still includes the v1 subdirectory:  
https://github.com/boostorg/process/blob/b529769eb5d08d9f95b0130fd2f1a49acad330f4/test/CMakeLists.txt#L2-L3  
I don't know if the two build systems are manually kept in sync and this was an oversight, or if there's tooling to regenerate the CMake files at some point?  
  
Anyway, I would advocate either reinstating the v1 tests (in which case I hope that this PR saves you some time), or deleting them altogether, including from the CMake file.

---

## Comment 1

> Username: klemens-morgenstern  
> Created_at: 2025-01-12 23:30:30 UTC  
> Url: https://github.com/boostorg/process/pull/450#issuecomment-2585963460  

Thank you!

---
