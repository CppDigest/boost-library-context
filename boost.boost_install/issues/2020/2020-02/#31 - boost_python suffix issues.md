# #31 - boost_python suffix issues [Closed]

> Username: mikepurvis  
> Created at: 2020-02-28 19:59:56 UTC  
> Updated at: 2021-04-27 03:05:06 UTC  
> Closed at: 2020-05-06 19:22:43 UTC  
> Url: https://github.com/boostorg/boost_install/issues/31  

Somewhat related to #21, but Boost 1.71 was just released into the repo for Ubuntu Focal LTS, and the story for `boost_python` is not great. From the perspective of an LTS user, over the years there have been five ways of linking to `boost_python` from CMake for a given Python X.Y:  
  
    1. find_package(Boost COMPONENTS python)  
    2. find_package(Boost COMPONENTS python-pyXY)  
    3. find_package(Boost COMPONENTS pythonX)  
    4. find_package(Boost COMPONENTS pythonX-pyXY)  
    5. find_package(Boost COMPONENTS pythonXY)  
  
Xenial's Boost (1.58) supported 1 and 2. Bionic's Boost (1.65.1) supported 1, 2, 3, and 4. Focal's Boost (currently 1.71) supports only 1 and 5. But using 1 on Focal gets you Python 3 whereas previously 1 got you Python 2. There is no one invocation that consistently gets you the Python 3 bindings.  
  
**My appeal is for Boost to continue supporting the `python3` and `numpy3` component names**, at least long enough for any software that wants to support multiple LTS releases from a single branch. _Particularly_ when you consider that `Boost_VERSION` has now been changed so that it is no longer usable for checks against systems old enough to not yet have `Boost_VERSION_MACRO` (for example, Ubuntu Bionic, where FindBoost is supplied by CMake 1.10).  
  
Launchpad ticket for the same issue: https://bugs.launchpad.net/ubuntu/+source/boost1.71/+bug/1865209

---

## Comment 1

> Username: pdimov  
> Created at: 2020-02-28 20:17:02 UTC  
> Url: https://github.com/boostorg/boost_install/issues/31#issuecomment-592709626  

I can't do anything about Boost 1.71, it has already been released (as was 1.72). I can make changes for 1.73, if the 1.72 behavior is problematic.

---

## Comment 2

> Username: mikepurvis  
> Created at: 2020-02-28 20:23:01 UTC  
> Updated at: 2020-02-28 21:30:35 UTC  
> Url: https://github.com/boostorg/boost_install/issues/31#issuecomment-592711872  

If you're willing to change 1.73 to support the `3` suffix for python components, it may be possible to have the Ubuntu Boost maintainer patch that change on to the version of 1.71 in the release.

---

## Comment 3

> Username: pdimov  
> Created at: 2020-02-28 20:33:26 UTC  
> Url: https://github.com/boostorg/boost_install/issues/31#issuecomment-592715829  

What are the actual XY versions on Focal?

---

## Comment 4

> Username: mikepurvis  
> Created at: 2020-02-28 21:17:21 UTC  
> Updated at: 2020-02-28 21:19:14 UTC  
> Url: https://github.com/boostorg/boost_install/issues/31#issuecomment-592732668  

Python 3.8 is the default: https://packages.ubuntu.com/source/focal/python3-defaults  
  
Python 2.7.17 is available in universe (unsupported): https://packages.ubuntu.com/source/focal/python-defaults  
  
But for the purposes of the Boost change, wouldn't you just be aliasing `python3` to do whatever `python` would have done, since `python` is now Python 3 anyway? Or would you want an additional check to ensure that someone who built Boost from source with Py2 support enabled couldn't accidentally link themselves to `libboost_python27` when specifying `python3`?

---

## Comment 5

> Username: pdimov  
> Created at: 2020-02-29 00:24:53 UTC  
> Url: https://github.com/boostorg/boost_install/issues/31#issuecomment-592789882  

It's a bit more involved. The `python` component, if more than one Python version variant is present, picks up one at random, unless `Boost_PYTHON_VERSION` is set. The format of `Boost_PYTHON_VERSION` is f.ex. `2.7` or `3.8`.  
  
For compatibility with FindBoost, components such as `pythonXY` are recognized and treated as if the component `python` is requested with `Boost_PYTHON_VERSION` set to `X.Y`.  
  
The logical behavior for the component `python3` (and `python2` for symmetry) would be for it to pick up one of the 3.x versions (but not the 2.x ones.) But this doesn't fit well within the current logic.  
  
I'll try to think of something.

---

## Comment 6

> Username: pdimov  
> Created at: 2020-03-08 03:39:59 UTC  
> Url: https://github.com/boostorg/boost_install/issues/31#issuecomment-596162965  

I've implemented support for the `python3` and `python2` components in https://github.com/boostorg/boost_install/commit/7d2b3bf3a25ecce33e256370e47d776d75685f7e. This change will go into the 1.73 release. I'm not 100% sure whether the patch will apply cleanly to 1.71; there were a lot of CMake config fixes in 1.72, but I think the Python part didn't change much, so it might work.

---

## Comment 7

> Username: mikepurvis  
> Created at: 2020-03-08 19:07:31 UTC  
> Url: https://github.com/boostorg/boost_install/issues/31#issuecomment-596240288  

Terrific, thanks! I'll look into the backporting story.

---

## Comment 8

> Username: PPeltzer  
> Created at: 2021-04-26 12:53:01 UTC  
> Updated at: 2021-04-26 12:53:11 UTC  
> Url: https://github.com/boostorg/boost_install/issues/31#issuecomment-826810699  

Has this actually been backported to 1.71? I'm running into some issues similar to that on Ubuntu 20.04 with the shipped Boost 1.71 (see above)

---

## Comment 9

> Username: mclow  
> Created at: 2021-04-26 16:02:58 UTC  
> Url: https://github.com/boostorg/boost_install/issues/31#issuecomment-826956659  

> Has this actually been backported to 1.71?  
  
In general, we do not make updated versions of old releases.  
Use 1.73 (or later), or apply the patch to your local copy of 1.71.

---

## Comment 10

> Username: mikepurvis  
> Created at: 2021-04-27 03:05:06 UTC  
> Url: https://github.com/boostorg/boost_install/issues/31#issuecomment-827279918  

@PPeltzer I wasn't able to get anything done on the Ubuntu side unfortunately— as far as calling in favours, there were more pressing issues with gnuarm and eigen3 that I _really_ needed addressed, so we basically just patched our CMakeList files to deal with it.
