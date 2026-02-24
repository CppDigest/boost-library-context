# #102 Feat: Add support to install FILE_SET too [Open]

> Username: ClausKlein  
> Created at: 2025-12-13 10:50:07 UTC  
> Updated at: 2026-01-08 23:53:08 UTC  
> Url: https://github.com/boostorg/cmake/pull/102  

with boost/any and boost/type_info there are at leaset 2 libs preparing CXX_MODULES  
  
This patch would enable to install it too

---

## Review 1 [Commented]

> Username: nigels-com  
> Created_at: 2025-12-14 07:49:31 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/cmake/pull/102#pullrequestreview-3574852263  

📁 include/BoostInstall.cmake

```diff
 338 |   endif()
 339 | 
 340 |+   if(NOT CMAKE_VERSION VERSION_LESS 3.28)
```

> Username: nigels-com  
> Created_at: 2025-12-14 07:49:31 UTC  
> Url: https://github.com/boostorg/cmake/pull/102#discussion_r2616809419  

Would suggest `VERSION_GREATER_EQUAL` rather than inverted logic.

> Username: ClausKlein  
> Created_at: 2025-12-16 13:23:29 UTC  
> Url: https://github.com/boostorg/cmake/pull/102#discussion_r2623273776  

done


---

## Review 2 [Commented]

> Username: nigels-com  
> Created_at: 2025-12-14 07:50:59 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/cmake/pull/102#pullrequestreview-3574852733  

📁 include/BoostInstall.cmake

```diff
 365 |-   install(EXPORT ${LIB}-targets DESTINATION "${CONFIG_INSTALL_DIR}" NAMESPACE Boost:: FILE ${LIB}-targets.cmake)
 391 |+   install(EXPORT ${LIB}-targets DESTINATION "${CONFIG_INSTALL_DIR}" NAMESPACE Boost:: FILE ${LIB}-targets.cmake
 392 |+     # XXX CXX_MODULES_DIRECTORY .
```

> Username: nigels-com  
> Created_at: 2025-12-14 07:50:58 UTC  
> Updated_at: 2025-12-14 07:50:59 UTC  
> Url: https://github.com/boostorg/cmake/pull/102#discussion_r2616810082  

Got plans for this?  Leave it out?

> Username: ClausKlein  
> Created_at: 2025-12-16 13:23:57 UTC  
> Updated_at: 2025-12-16 13:23:58 UTC  
> Url: https://github.com/boostorg/cmake/pull/102#discussion_r2623275345  

found a better solution


---

## Review 3 [Commented]

> Username: pdimov  
> Created_at: 2025-12-16 14:18:43 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/cmake/pull/102#pullrequestreview-3583336770  

📁 include/BoostInstall.cmake

```diff
 343 |+       boost_message(DEBUG "boost_install_target: '${__TARGET}' has INTERFACE_CXX_MODULE_SETS=${INTERFACE_CXX_MODULE_SETS}")
 344 |+       set(__INSTALL_CXX_MODULES FILE_SET ${INTERFACE_CXX_MODULE_SETS} DESTINATION ${CONFIG_INSTALL_DIR})
 345 |+       set(__INSTALL_CXX_MODULES_BMI CXX_MODULES_BMI DESTINATION ${CONFIG_INSTALL_DIR}/bmi-${CMAKE_CXX_COMPILER_ID}_$<CONFIG>)
```

> Username: pdimov  
> Created_at: 2025-12-16 14:18:43 UTC  
> Updated_at: 2025-12-16 14:18:44 UTC  
> Url: https://github.com/boostorg/cmake/pull/102#discussion_r2623473143  

I'm not sure we need to be installing these. From what I read, it's not a recommended practice to do so.

> Username: ClausKlein  
> Created_at: 2025-12-17 09:02:56 UTC  
> Updated_at: 2025-12-17 09:02:57 UTC  
> Url: https://github.com/boostorg/cmake/pull/102#discussion_r2626176633  

OK


---

## Comment 4

> Username: ClausKlein  
> Created_at: 2026-01-08 23:53:08 UTC  
> Url: https://github.com/boostorg/cmake/pull/102#issuecomment-3726416000  

see too https://github.com/ClausKlein/boost-cmake/pull/11

---
