# #6 - BoostConfig.cmake should define version numbers etc. [Closed]

> Username: sophonet  
> Created at: 2019-04-27 11:11:16 UTC  
> Updated at: 2019-05-25 01:31:41 UTC  
> Closed at: 2019-05-25 01:31:41 UTC  
> Url: https://github.com/boostorg/boost_install/issues/6  

When looking at https://cmake.org/cmake/help/latest/module/FindBoost.html, the BoostConfig.cmake version should also define  
  
Boost_VERSION          - BOOST_VERSION value from boost/version.hpp  
Boost_LIB_VERSION      - Version string appended to library filenames  
Boost_MAJOR_VERSION    - Boost major version number (X in X.y.z)  
Boost_MINOR_VERSION    - Boost minor version number (Y in x.Y.z)  
Boost_SUBMINOR_VERSION - Boost subminor version number (Z in x.y.Z)  
Boost_VERSION_STRING   - Boost version number in x.y.z format  
  
Other variables like Boost_C_FOUND etc. might not be necessary anymore with imported targets since this can be done with IF(TARGET Boost::C) - but for backward compatibility they would not hurt (otherwise, all CMake projects using Boost need to be modified for after version 1.70)

---

## Comment 1

> Username: pdimov  
> Created at: 2019-04-27 13:49:25 UTC  
> Url: https://github.com/boostorg/boost_install/issues/6#issuecomment-487287581  

`find_package` should automatically define the version variables `Boost_VERSION`, `Boost_VERSION_MAJOR`, `Boost_VERSION_MINOR`, and others - see https://cmake.org/cmake/help/v3.14/command/find_package.html#version-selection

---

## Comment 2

> Username: sophonet  
> Created at: 2019-04-27 14:48:17 UTC  
> Url: https://github.com/boostorg/boost_install/issues/6#issuecomment-487292108  

Indeed, that seems to be correct, sorry for the confusion. So then it's just a wish to define the legacy variables BOOST_C_LIBRARY and Boost_INCLUDE_DIR (and maybe others from the old CMake FindBoost) to keep backward compatibility.

---

## Comment 3

> Username: pdimov  
> Created at: 2019-04-27 15:11:19 UTC  
> Url: https://github.com/boostorg/boost_install/issues/6#issuecomment-487293866  

I've been asked about the old-style variables before, but I'm not sure what's the proper place to define them, in BoostConfig or in FindBoost. @dennisklein, what do you think?

---

## Comment 4

> Username: sophonet  
> Created at: 2019-04-27 15:21:47 UTC  
> Url: https://github.com/boostorg/boost_install/issues/6#issuecomment-487294694  

In a local patch on my system, I have added a print statement `set(Boost_$(name:U)_LIBRARY $(target))` near the end of boost-install.jam -> generate-make-config- ( target : sources * : properties * ), which seems to do the trick for the old library name.  
  
BoostConfig.cmake could be the right place for Boost_INCLUDE_DIR, but currently, it does not contain generated information but has static content. Currently, on my system, I have inserted a hack  
  
```  
if(NOT Boost_INCLUDE_DIR)  
  set(Boost_INCLUDE_DIR \"${_BOOST_INCLUDEDIR}\")  
endif()  
```  
  
in the same function generate-cmake-config... but probably, this can be improved.

---

## Comment 5

> Username: pdimov  
> Created at: 2019-04-27 15:41:18 UTC  
> Url: https://github.com/boostorg/boost_install/issues/6#issuecomment-487296209  

These both can be done in BoostConfig; `Boost_${__boost_comp}_LIBRARY` can be set to `Boost::${__boost_comp}` in the `foreach(__boost_comp)` loop (when the component is found), and the include directory can be extracted from the Boost::headers target.

---

## Comment 6

> Username: dennisklein  
> Created at: 2019-04-27 19:57:59 UTC  
> Updated at: 2019-04-28 12:13:38 UTC  
> Url: https://github.com/boostorg/boost_install/issues/6#issuecomment-487315217  

> I've been asked about the old-style variables before, but I'm not sure what's the proper place to define them, in BoostConfig or in FindBoost. @dennisklein, what do you think?  
  
* FindBoost already defines them for module mode, and so everything stays together in one place (which is also nice, if they get deprecated/removed at some point in the future).  
* Defines in FindBoost that logically belong to BoostConfig would be missed sometimes, because depending on how the user calls `find_package(Boost ...)`, two call stacks are possible:  
   * `find_package` calls `FindBoost` calls `BoostConfig`  
   * `find_package` calls `BoostConfig`  
  
Considering the above, in my opinion, we should go for defining old-style variables in BoostConfig. Regarding the version variables please also consider the variable names `find_package` config mode chooses are different from the ones FindBoost defines (`Boost_MAJOR_VERSION` vs `Boost_VERSION_MAJOR`). Also note that the values of `Boost_VERSION` are different (which will be fixed in FindBoost in cmake 3.15, if my pending MR is merged) - FindBoost currently sets it to `107000` and BoostConfig to `1.70.0` (the latter is more idiomatic cmake, imho, we should stick with dotted format).

---

## Comment 7

> Username: dennisklein  
> Created at: 2019-05-13 16:28:01 UTC  
> Url: https://github.com/boostorg/boost_install/issues/6#issuecomment-491893309  

For reference: https://gitlab.kitware.com/cmake/cmake/merge_requests/3317  
  
If the above PR is merged, in CMake `3.15` the `FindBoost.cmake` module will report those version variables:  
  
```  
  Boost_VERSION_MACRO    - BOOST_VERSION value from boost/version.hpp  
  Boost_VERSION_STRING   - Boost version number in x.y.z format  
  Boost_VERSION          - if CMP0093 NEW => same as Boost_VERSION_STRING  
                           if CMP0093 OLD or unset => same as Boost_VERSION_MACRO  
  Boost_LIB_VERSION      - Version string appended to library filenames  
  Boost_VERSION_MAJOR    - Boost major version number (X in X.y.z)  
                           alias: Boost_MAJOR_VERSION  
  Boost_VERSION_MINOR    - Boost minor version number (Y in x.Y.z)  
                           alias: Boost_MINOR_VERSION  
  Boost_VERSION_PATCH    - Boost subminor version number (Z in x.y.Z)  
                           alias: Boost_SUBMINOR_VERSION  
  Boost_VERSION_COUNT    - Amount of version components (3)  
```  
  
`Boost_VERSION_MAJOR/MINOR/PATCH/COUNT` are automatically populated by CMake (depending on the value of `Boost_VERSION`). If you decide to define the old-style module mode variables as requested by @sophonet, you may consider to also include `Boost_VERSION_MACRO` (in addition to the list in the first post).

---

## Comment 8

> Username: pdimov  
> Created at: 2019-05-13 17:06:05 UTC  
> Url: https://github.com/boostorg/boost_install/issues/6#issuecomment-491906463  

I don't think that defining `Boost_VERSION_MACRO` in `BoostConfig` will ever be needed. The point of the compatibility variables is to avoid breaking too many packages that currently do `find_package(Boost)` and get `BoostConfig` because of the delegation. Since `Boost_VERSION_MACRO` is a new variable, it's not possible for old packages to depend on it, so backward compatibility is not a concern.  
  
I've already added `Boost_INCLUDE_DIRS`, `Boost_LIBRARIES`, `Boost_<C>_LIBRARY` as requested here and in #7. I haven't added `Boost_MAJOR_VERSION` et al yet; I would prefer not to, but if too many packages depend on them, probably will.

---

## Comment 9

> Username: pdimov  
> Created at: 2019-05-25 01:31:41 UTC  
> Url: https://github.com/boostorg/boost_install/issues/6#issuecomment-495824141  

Added version variables in https://github.com/boostorg/boost_install/commit/c010d4739270a4fbeecc03af1410e48dcc45e20f. Please reopen if there's anything else.
