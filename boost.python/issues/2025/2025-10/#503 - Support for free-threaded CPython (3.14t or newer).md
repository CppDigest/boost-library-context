# #503 - Support for free-threaded CPython (3.14t or newer) [Closed]

> Username: nascheme  
> Created at: 2025-10-29 18:22:07 UTC  
> Updated at: 2026-02-09 21:46:23 UTC  
> Closed at: 2026-02-09 21:46:23 UTC  
> Url: https://github.com/boostorg/python/issues/503  

This issue is intended to track the efforts of adding support for the free-threaded build for CPython (3.14t and potentially 3.13t).  The [Python free-threading guide](https://py-free-threading.github.io/porting/) contains some helpful advice on approaching this task.  Based on  what has been done for pybind11, I think the following steps make sense:  
  
- Add mechanism to set the `Py_MOD_GIL_NOT_USED`.  I have a patch that does something similar to what pybind11 does: add a variadic macro with an optional flag so extensions can opt-in to free-threaded support  
- Replace borrow based APIs with strong reference ones (e.g. `PyDict_GetItemRef`)  
- Use a mutex to protect global state (e.g. the registry)  
- Fix references to `ob_refcnt`, don't use `Py_REFCNT()` as an l-value.  
- Modify tests so we can run them in parallel, run with the TSAN build to find races  
- Fixes to "faber" to work with new versions of Python.  The most recent release of faber fixes most things, I believe.  
  
I have code that does most of this, I will create a PR.  I'm still working on some crash bugs when running with 3.14t.  If you run the Boost.Python tests with a single thread, they pass.  So that's encouraging.  
  
Related issues:  
- https://github.com/boostorg/python/issues/458  
  
Related PRs:  
- https://github.com/boostorg/python/pull/504

---

## Comment 1

> Username: rgommers  
> Created at: 2026-02-09 10:14:28 UTC  
> Url: https://github.com/boostorg/python/issues/503#issuecomment-3870755223  

@nascheme this can be closed right, after your work from gh-504 was merged?

---

## Comment 2

> Username: nascheme  
> Created at: 2026-02-09 21:46:23 UTC  
> Url: https://github.com/boostorg/python/issues/503#issuecomment-3874037311  

Yes, I'll close.
