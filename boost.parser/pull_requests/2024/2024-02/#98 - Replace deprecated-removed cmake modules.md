# #98 Replace deprecated/removed cmake modules [Merged]

> Username: mborland  
> Created at: 2024-02-21 09:40:43 UTC  
> Updated at: 2024-02-27 14:22:24 UTC  
> Merged at: 2024-02-27 02:39:09 UTC  
> Closed at: 2024-02-27 02:39:09 UTC  
> Url: https://github.com/boostorg/parser/pull/98  

Fixes: "Policy CMP0148 is not set: The FindPythonInterp and FindPythonLibs modules  
are removed.  Run "cmake --help-policy CMP0148" for policy details.  Use  
the cmake_policy command to set the policy and suppress this warning."  
  
[PythonInterp was deprecated in 3.12 and removed in 3.27](https://cmake.org/cmake/help/latest/module/FindPythonInterp.html)

---

## Comment 1

> Username: Flamefire  
> Created_at: 2024-02-24 16:16:16 UTC  
> Url: https://github.com/boostorg/parser/pull/98#issuecomment-1962413005  

Why is this even required? Neither `PYTHON_EXECUTABLE` nor `PYTHON3_EXECUTABLE` are used so I'd just remove this part of the code which looks like some leftover

---

## Comment 2

> Username: tzlaine  
> Created_at: 2024-02-24 21:03:06 UTC  
> Url: https://github.com/boostorg/parser/pull/98#issuecomment-1962730847  

That's a good point; it's not required here.  It's just part of some boilerplate CMake that I copy around from project to project, and I never removed it here, nor used it.

---

## Review 3 [Commented]

> Username: Flamefire  
> Created_at: 2024-02-26 08:48:35 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/parser/pull/98#pullrequestreview-1900270467  

📁 CMakeLists.txt

```diff
  32 |+ if (Python3_Interpreter_FOUND)
  33 |+     message("-- Found Python ${Python3_VERSION} (${Python3_EXECUTABLE})")
  34 | endif ()
```

> Username: Flamefire  
> Created_at: 2024-02-26 08:48:35 UTC  
> Url: https://github.com/boostorg/parser/pull/98#discussion_r1502232258  

Unused, so just remove:  
  
```suggestion  
```


---

## Comment 4

> Username: tzlaine  
> Created_at: 2024-02-27 02:39:05 UTC  
> Url: https://github.com/boostorg/parser/pull/98#issuecomment-1965684226  

Thanks!

---
