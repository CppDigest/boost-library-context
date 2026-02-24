# #201 explicitly look for Qt4 and not for Qt in general [Closed]

> Username: f-koehler  
> Created at: 2014-07-29 11:47:04 UTC  
> Updated at: 2014-07-30 01:39:16 UTC  
> Closed at: 2014-07-29 15:45:00 UTC  
> Url: https://github.com/boostorg/compute/pull/201  

I had a problem building the Qt examples, so I looked into the CMakeLists.  
  
I use CMake 3.0.0 and have both Qt4 and Qt5 installed. QT_USE_FILE is defined by the FindQt4.cmake module that comes shipped with CMake. In versions older than 3.0.0 the FindQt.cmake module uses FindQt4.cmake to find Qt. In newer versions CMake will look for Qt5 so that QT_USE_FILE is not defined. By directly looking for Qt4 by calling find_package(Qt REQUIRED COMPONENTS QtCore QtGui QtOpenGL) everything works fine, even when using CMake 2.8.8.

---

## Comment 1

> Username: coveralls  
> Created_at: 2014-07-29 12:03:04 UTC  
> Url: https://github.com/boostorg/compute/pull/201#issuecomment-50466934  

[![Coverage Status](https://coveralls.io/builds/1018538/badge)](https://coveralls.io/builds/1018538)  
  
Coverage remained the same when pulling **b70df00ce45454e43cdfab850214a1db31e68ab7 on f-koehler:master** into **f50e9c011005ef27a63760fc6b6d7a3f092f0b5d on kylelutz:master**.

---

## Comment 2

> Username: f-koehler  
> Created_at: 2014-07-29 12:47:05 UTC  
> Url: https://github.com/boostorg/compute/pull/201#issuecomment-50471084  

There is probably a more elegant way...

---

## Comment 3

> Username: f-koehler  
> Created_at: 2014-07-29 13:47:59 UTC  
> Url: https://github.com/boostorg/compute/pull/201#issuecomment-50477793  

Reopened this, as I can't find an easier way. QApplication moved from <QtGui> to <QtWidgets>. I thought I could let CMake search for Qt5 first and let it use Qt4 if it is not found (this actually works fine), but a change in the sources of the examples that use Qt would be required. This could be done by moving the Qt include lines in the sources to a header file that would be configured by CMake. The easier way is to insist on using Qt4.

---

## Comment 4

> Username: coveralls  
> Created_at: 2014-07-29 14:34:02 UTC  
> Url: https://github.com/boostorg/compute/pull/201#issuecomment-50483869  

[![Coverage Status](https://coveralls.io/builds/1019025/badge)](https://coveralls.io/builds/1019025)  
  
Coverage remained the same when pulling **b70df00ce45454e43cdfab850214a1db31e68ab7 on f-koehler:master** into **f50e9c011005ef27a63760fc6b6d7a3f092f0b5d on kylelutz:master**.

---

## Comment 5

> Username: kylelutz  
> Created_at: 2014-07-29 14:43:58 UTC  
> Url: https://github.com/boostorg/compute/pull/201#issuecomment-50485451  

Cool! Ideally we'd support Qt5 too but this is good for now.  
  
Also, can you make the pull-request against the `develop` branch? (unfortunately requires closing this and opening a new one). There's a little documentation about this in the developers guide (https://github.com/kylelutz/compute/wiki/Developer%27s-Guide) on the wiki. After that it should be good to merge.  
  
Thanks!

---

## Comment 6

> Username: f-koehler  
> Created_at: 2014-07-29 15:45:00 UTC  
> Url: https://github.com/boostorg/compute/pull/201#issuecomment-50494947  

Ok, I am new to pull requests (most times I work alone on my simulations and stuff). One could probably just add a new CMake option like BOOST_COMPUTE_HAVE_QT5. This would require a config.hpp.in file for the examples.

---

## Comment 7

> Username: kylelutz  
> Created_at: 2014-07-30 01:39:16 UTC  
> Url: https://github.com/boostorg/compute/pull/201#issuecomment-50563955  

I made an issue for Qt5 support (#204). I don't think we'd need to add a CMake option as we can use the `QT_VERSION` macro defined in `<QtGlobal>` (which is present in both Qt4 and Qt5).

---
