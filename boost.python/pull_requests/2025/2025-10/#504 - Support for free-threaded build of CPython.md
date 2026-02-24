# #504 Support for free-threaded build of CPython [Closed]

> Username: nascheme  
> Created at: 2025-10-31 17:13:53 UTC  
> Updated at: 2025-12-08 16:45:47 UTC  
> Closed at: 2025-12-08 16:45:46 UTC  
> Url: https://github.com/boostorg/python/pull/504  

Summary of changes:  
  
- Add mechanism to set the Py_MOD_GIL_NOT_USED. It works similar to what pybind11 does: add a variadic macro with an optional flag so extensions can opt-in to free-threaded support  
- Replace borrow based APIs with strong reference ones (e.g. PyDict_GetItemRef).  
- Use a mutex to protect global state (e.g. the registry).  To avoid deadlocks and keep the code simple, there is a single global recursive lock.  We can refine this later (split it into the multiple locks, use critical sections, etc).  
- Fix references to ob_refcnt, don't use Py_REFCNT() as an l-value.  
- Add work-around for crash in `~object_base()`.  Calling `Py_DECREF()` with no thread-state is not allowed.  
  
Building this requires a [fix to faber](https://github.com/stefanseefeld/faber/pull/37).  I found building Boost.Python very tricky (likely because I'm not familiar with b2 and faber).  I'll share my build scripts in case they help someone else (need to tidy them first).  
  
There is [another branch](https://github.com/nascheme/boost-python/tree/ft_fixes_pytest) that includes doctests converted to use pytest.  I only did a subset of the tests (using Claude to assist in conversion).  The goal is to use pytest-run-parallel and try to trigger data races or other issues.

---

## Comment 1

> Username: stefanseefeld  
> Created_at: 2025-11-06 21:01:11 UTC  
> Updated_at: 2025-11-06 21:01:58 UTC  
> Url: https://github.com/boostorg/python/pull/504#issuecomment-3499377278  

@nascheme I have just pushed a new faber release (0.5.10) including your contributions. Let's see whether they help with this.  
(But it seems this needs to be rebased first, to resolve some merge conflicts.)

---

## Comment 2

> Username: nascheme  
> Created_at: 2025-11-07 00:15:31 UTC  
> Updated_at: 2025-11-07 00:16:52 UTC  
> Url: https://github.com/boostorg/python/pull/504#issuecomment-3499927027  

Thanks for merging the faber changes.  My latest push adds the nogil option for BOOST_PYTHON_MODULE_INIT.  It could be simplified if we now require C+11 since there is quite a bit of code to support C+98.  The code for this follows closely how pybind11 implements the corresponding option.  
  
We might follow [what's done by pyo3](https://github.com/PyO3/pyo3/pull/5564) in regard to this option and make the default to be true.  
  
I've tested with a free-threaded Python build with thread-sanitizer (TSAN) enabled.  Running the [pytest unit tests](https://github.com/nascheme/boost-python/tree/ft_fixes_pytest) using pytest-run-parallel generates no TSAN warnings.  That definitely does not mean we are safe from data races but it gives me some re-assurance we have the right locks in place.

---

## Comment 3

> Username: nascheme  
> Created_at: 2025-11-07 00:25:50 UTC  
> Url: https://github.com/boostorg/python/pull/504#issuecomment-3499949840  

For the `~object_base()` issue, I found that [scipy ran into a similar kind of issue](https://github.com/scipy/scipy/pull/21218#pullrequestreview-2466103157).  The comment from Sam Gross:  
> It's not just a matter of a Py_INCREF to leak them, you don't want to call Py_DECREF from the destructors of variables with global lifetimes. Even if it doesn't trigger the destructor, the Py_DECREF after Python is finalized or without a valid thread state isn't safe, especially in debug builds or the free-threaded build.  
  
The `PyThreadState_GetUnchecked() == NULL`  check is my C programmer fix to this problem.  A different option might be to use PyModules's [m_free](https://github.com/Quansight-Labs/uarray/pull/256).  To me, just leaking some memory here is okay since Python is shutting down anyhow.

---

## Comment 4

> Username: stefanseefeld  
> Created_at: 2025-11-07 01:17:52 UTC  
> Url: https://github.com/boostorg/python/pull/504#issuecomment-3500053991  

Thanks for all the context. The PR is still marked as "cannot be rebased due to conflicts". Can you retry rebasing it manually ? (In general, I prefer to rebase a feature branch repeatedly over merging the target branch into the feature branch, as the former allows to fast-forward merge at the end, while the latter doesn't.)

---

## Comment 5

> Username: nascheme  
> Created_at: 2025-11-07 02:41:10 UTC  
> Url: https://github.com/boostorg/python/pull/504#issuecomment-3500265808  

Okay.  Are you planning to fast-forward merge without squashing?  If so, I'll rebase and also cleanup the commits into more logical ones.  If you don't squash them then they could be improved.

---

## Comment 6

> Username: stefanseefeld  
> Created_at: 2025-11-07 02:55:23 UTC  
> Url: https://github.com/boostorg/python/pull/504#issuecomment-3500319616  

I don't know yet. I need to understand the changes better, and, if the commits are self-contained and complete, it might actually be better to keep them separate to make it easier to understand (and possibly debug) them.  
  
But at the very least, we need to make them conflict-free to be able to apply and test them.

---

## Comment 7

> Username: nascheme  
> Created_at: 2025-11-10 21:13:34 UTC  
> Url: https://github.com/boostorg/python/pull/504#issuecomment-3513918151  

I rebased the branch and cleaned up each commit.  Merging this without squashing would be okay now, IMHO.  If you want to split this into multiple PRs, let me know.  
  
The changes are nearly all conditional on `Py_GIL_DISABLED` and so don't affect the GIL-enabled (default) version of Python.   The added locking with PyMutex is conditional on `Py_GIL_DISABLED` as well and is a non-op in the default build.

---

## Comment 8

> Username: stefanseefeld  
> Created_at: 2025-11-10 22:08:39 UTC  
> Url: https://github.com/boostorg/python/pull/504#issuecomment-3514089324  

Thanks for the update. We should then also add at least one CI build variant that uses `Py_GIL_DISABLED`, to test the changes, shouldn't we ?

---

## Comment 9

> Username: nascheme  
> Created_at: 2025-11-17 21:42:01 UTC  
> Url: https://github.com/boostorg/python/pull/504#issuecomment-3543955291  

> We should then also add at least one CI build variant that uses Py_GIL_DISABLED, to test the changes, shouldn't we ?  
  
Hello @stefanseefeld, I think that's a good idea.  I have a revised CI action script to test with 3.14t as well.  If we continue with the matrix approach, the options are:  
  
-  cxx: g++/clang++   
- std: c++11, c++14, c++17  
- python: 3.10, 3.11, 3.12, 3.13, 3.14, 3.14t  
  
that makes a 36 combinations.  Too many, I think.  
  
  
A different idea I had was to have two Linux CI scripts, one to test compiler and language standard combos, with the options being:  
-  cxx: g++/clang++   
- std: c++11, c++14, c++17  
- python: 3.14  
  
Then, a second "python versions" CI script, suggested combos:  
-  cxx: g++   
- std: c++11  
- python: 3.10, 3.11, 3.12, 3.13, 3.14, 3.14t  
  
Together than makes a total of 12 runs.  Another idea would be to set one of the CI script to be "on: workflow_dispatch" and just manually run it before a release.  Since the previous CI was only testing with one Python version, maybe this is still overkill?  What do you prefer?

---

## Comment 10

> Username: stefanseefeld  
> Created_at: 2025-11-17 22:10:57 UTC  
> Url: https://github.com/boostorg/python/pull/504#issuecomment-3544053632  

Hi @nascheme , thanks for following up ! I agree that a full ("dense") matrix is overkill, so we need to look into how to make it "sparse". (In terms of coverage I expect users of the new Python features will likely also use newer compilers and C++ standards, so it might be enough to test the new features with the most recent set of compilers & flags.)  
I'll look a bit into different ways of generating build parameters with this yaml language, so perhaps there are ways to write "generators" to produce triplets (cxx, std, python), so we can produce sparse matrices within a single script.

---

## Comment 11

> Username: nascheme  
> Created_at: 2025-11-17 23:36:11 UTC  
> Url: https://github.com/boostorg/python/pull/504#issuecomment-3544298042  

I've revised the CI workflows to use setup-python.  Getting faber to use the correct include and linker flags took some work.  Ideally I think faber could be a bit smarter to figure this out on its own (using python3-config).  
  
I changed test-ubuntu to test with 3.14 only.  I added test-ubuntu-py-vers which tests with a set of Python versions (3.10 to 3.14).

---

## Review 12 [Commented]

> Username: stefanseefeld  
> Created_at: 2025-11-27 22:57:27 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/python/pull/504#pullrequestreview-3517050890  

📁 .github/workflows/test-ubuntu.yml

```diff
  20 |+           # Also test with free-threaded build of Python
  21 |+           - python-version: '3.14t'
  22 |+             cxx: clang+
```

> Username: stefanseefeld  
> Created_at: 2025-11-27 22:57:27 UTC  
> Updated_at: 2025-11-27 22:57:39 UTC  
> Url: https://github.com/boostorg/python/pull/504#discussion_r2570072874  

that looks like a typo. The value should probably be `clang++`.

> Username: nascheme  
> Created_at: 2025-11-28 01:26:53 UTC  
> Updated_at: 2025-11-28 01:26:54 UTC  
> Url: https://github.com/boostorg/python/pull/504#discussion_r2570245903  

Yup, I change that line from g++ to clang++ at the last moment (thinking maybe Clang will be a bit faster for CI runs).  Fixed now but a lesson to not change things at the last minute, after you tested them.


---

## Comment 13

> Username: stefanseefeld  
> Created_at: 2025-11-28 16:12:25 UTC  
> Url: https://github.com/boostorg/python/pull/504#issuecomment-3589831118  

This looks great now. One little thing I'd like to address before this can be merged is that with the above we no longer test Python 2 on Ubuntu. I'd like to keep at least one CI build on Ubuntu run against Python 2.7, to make sure we don't accidentally break backward compatibility. (I'll look into this myself over the next couple of days, if you don't have the time to do it.)

---

## Comment 14

> Username: stefanseefeld  
> Created_at: 2025-11-29 03:11:38 UTC  
> Updated_at: 2025-11-29 03:12:16 UTC  
> Url: https://github.com/boostorg/python/pull/504#issuecomment-3590940561  

Little update: I have been trying to add a python 2.7 variant to the mix, and got test failures.  
If you go back to commit https://github.com/boostorg/python/pull/504/commits/80731af9ee0541017fbcdbf920a07731388342e0 and try to build that (which used the original python2/python3 set of build variants), you'll notice that the python2 variants fail, due to a change to the module initialization API.  
  
We need to fix that (and make sure to test with python 2.7 as there are surely some users who still need support for that) before this can be merged.

---

## Comment 15

> Username: nascheme  
> Created_at: 2025-12-02 00:57:52 UTC  
> Url: https://github.com/boostorg/python/pull/504#issuecomment-3599634105  

Okay.  I have to wonder who is still running Python 2.7 since it is EOL 5 years ago and is not getting security updates.  However, since you wish to still support it, I've added a `test-ubuntu-py2.yml` workflow file to test with it.  This seems simpler than modifying the `test-ubuntu` workflow to test with either 3.x or 2.7.  Once you don't need to support Python 2 any longer, you can just delete that file.  
  
I made a fix to the `BOOST_PYTHON_MODULE_INIT` and the unit test for it so that it still works with Python 2.7.

---

## Comment 16

> Username: stefanseefeld  
> Created_at: 2025-12-08 16:45:46 UTC  
> Url: https://github.com/boostorg/python/pull/504#issuecomment-3627977637  

Thanks @nascheme ! I've merged your work (after squashing some of the commits).

---
