# #321 Properly detect python header files [Closed]

> Username: yoonghm  
> Created at: 2019-08-19 15:51:55 UTC  
> Updated at: 2023-12-06 02:47:27 UTC  
> Closed at: 2023-12-06 02:47:27 UTC  
> Url: https://github.com/boostorg/boost/pull/321  

The original code use `python -c "import sys; print(sys.prefix)"` which did not show actual directory for include file.  
  
The proper way is `python -c "from sysconfig import get_paths as gp; print(gp()['include'])"`

---

## Comment 1

> Username: ali5h  
> Created_at: 2019-08-25 22:34:02 UTC  
> Url: https://github.com/boostorg/boost/pull/321#issuecomment-524669931  

with this patch i see the change in `project-config.jam` but `g++` calls still use the wrong include path for python include.

---

## Comment 2

> Username: mclow  
> Created_at: 2019-08-28 18:24:30 UTC  
> Url: https://github.com/boostorg/boost/pull/321#issuecomment-525865404  

A nit: We don't take PRs against the `master` branch. We take them against `develop`, then let the tests run, and merge to `master`.

---

## Comment 3

> Username: jsharpe  
> Created_at: 2019-12-03 12:02:09 UTC  
> Url: https://github.com/boostorg/boost/pull/321#issuecomment-561137425  

This patch is incorrect - the real issue looks to be in boost build - if you don't fully specify version, includes and libraries in the python script it ignores the given version and tries to infer it from sys.version which is missing the m part of the version string.

---

## Comment 4

> Username: jsharpe  
> Created_at: 2019-12-03 12:56:11 UTC  
> Url: https://github.com/boostorg/boost/pull/321#issuecomment-561154918  

See https://github.com/boostorg/build/issues/481

---

## Comment 5

> Username: likan999  
> Created_at: 2020-04-29 00:24:57 UTC  
> Url: https://github.com/boostorg/boost/pull/321#issuecomment-620922052  

I don't see how this could solve the problem. The issue is python.jam that sets the path to `$(prefix)/include/python$(version)`.

---

## Review 6 [Changes requested]

> Username: Flamefire  
> Created_at: 2022-12-16 16:25:20 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/boost/pull/321#pullrequestreview-1221120293  

The code looks correct as-is: `$PYTHON_ROOT` gets set to the root of the Python install, so this PR would set it to a wrong value

---
