# #393 Replace deprecated find_package for python [Merged]

> Username: mborland  
> Created at: 2023-12-12 11:55:57 UTC  
> Updated at: 2024-04-26 11:09:02 UTC  
> Merged at: 2024-04-23 12:11:46 UTC  
> Closed at: 2024-04-23 12:11:46 UTC  
> Url: https://github.com/boostorg/histogram/pull/393  

Closes: #392

---

## Review 1 [Commented]

> Username: henryiii  
> Created_at: 2024-03-30 05:25:23 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/histogram/pull/393#pullrequestreview-1969822954  

📁 test/CMakeLists.txt

```diff
   5 |- find_package(PythonInterp)
   5 |+ find_package(Python3)
   6 | if (PYTHONINTERP_FOUND)
```

> Username: henryiii  
> Created_at: 2024-03-30 05:25:22 UTC  
> Updated_at: 2024-03-30 05:25:23 UTC  
> Url: https://github.com/boostorg/histogram/pull/393#discussion_r1545127338  

This is not the same variable. And this requires CMake 3.15+.

> Username: HDembinski  
> Created_at: 2024-04-23 11:18:38 UTC  
> Url: https://github.com/boostorg/histogram/pull/393#discussion_r1576081700  

The docs said it was added in CMake 3.12. I think it is safe to make this the new minimum version. The cmake build is not official, so we don't have a policy what minimum cmake version we have to support.

> Username: henryiii  
> Created_at: 2024-04-23 13:52:50 UTC  
> Url: https://github.com/boostorg/histogram/pull/393#discussion_r1576305095  

PYTHONINTERP_FOUND will not be set by FindPython3 or FindPython. Neither will PYTHON_EXECUTABLE. Though you are right about 3.12 being the first version; it wasn't very usable until 3.15-3.18 for building Python modules, but for just looking for the Python executable, 3.12 should work, I think.  
  
Also, you need to add `COMPONENTS Interpreter`.

> Username: henryiii  
> Created_at: 2024-04-23 13:54:03 UTC  
> Url: https://github.com/boostorg/histogram/pull/393#discussion_r1576307376  

```diff  
+if (Python3_FOUND)  
```  
  
This is the new variable.


---

## Review 2 [Commented]

> Username: henryiii  
> Created_at: 2024-04-23 13:55:04 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/histogram/pull/393#pullrequestreview-2017347642  

📁 test/CMakeLists.txt

```diff
   6 | if (PYTHONINTERP_FOUND)
   7 |   # checks that b2 and cmake are in sync
   8 |   add_test(NAME runpy-${PROJECT_NAME}_check_build_system COMMAND ${PYTHON_EXECUTABLE} ${CMAKE_CURRENT_SOURCE_DIR}/check_build_system.py)
```

> Username: henryiii  
> Created_at: 2024-04-23 13:55:04 UTC  
> Url: https://github.com/boostorg/histogram/pull/393#discussion_r1576309015  

You should be able to use the target here, `Python3::Interpreter`. If not, `${Python3_EXECUTABLE}` is the new variable name.

> Username: HDembinski  
> Created_at: 2024-04-25 08:19:08 UTC  
> Url: https://github.com/boostorg/histogram/pull/393#discussion_r1579073858  

`find_package(Python3::Interpreter)` did not work, but `find_package(Python3 COMPONENTS Interpreter)` does. I will implement that in another minor PR.

> Username: henryiii  
> Created_at: 2024-04-25 08:43:46 UTC  
> Url: https://github.com/boostorg/histogram/pull/393#discussion_r1579108006  

That’s the default so it’s fine either way, what I meant was `${Python3_EXECUTABLE}` (the variable) can be replaced by `Python3::Interpreter` (a CMake target).

> Username: henryiii  
> Created_at: 2024-04-25 08:44:33 UTC  
> Url: https://github.com/boostorg/histogram/pull/393#discussion_r1579109051  

(After COMMAND a target is supported)

> Username: HDembinski  
> Created_at: 2024-04-26 11:09:01 UTC  
> Updated_at: 2024-04-26 11:09:02 UTC  
> Url: https://github.com/boostorg/histogram/pull/393#discussion_r1580874944  

Ok, I see.


---
