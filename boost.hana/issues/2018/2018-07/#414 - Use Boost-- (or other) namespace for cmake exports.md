# #414 - Use Boost:: (or other) namespace for cmake exports [Open]

> Username: urp  
> Created at: 2018-07-09 20:57:09 UTC  
> Updated at: 2018-08-06 15:06:46 UTC  
> Url: https://github.com/boostorg/hana/issues/414  

* The `NAMESPACE` keyword of the [`install( EXPORT )` command](https://github.com/boostorg/hana/blob/6187d448df49db085d4d7ad63d3a5f479594c5cf/CMakeLists.txt#L63) should be used. Targets in namespaces containing `::` can be distinguished from libraries resulting in better error messages (popping up during configuration, instead of linking) if the target was not found.  
  
* Using `Boost::` as namespace would make the resulting target name coherent with the result of `find_package( Boost )` which  ~~creates~~ should create an imported target `Boost::hana` (currently, only library targets [are exported as `Boost::<Component>`](https://cmake.org/cmake/help/v3.9/module/FindBoost.html))  
  
Thank you for the nice `CMakeLists.txt` ;)

---

## Comment 1

> Username: urp  
> Created at: 2018-07-09 21:35:14 UTC  
> Updated at: 2018-07-09 21:37:29 UTC  
> Url: https://github.com/boostorg/hana/issues/414#issuecomment-403627980  

Addition of  `add_library( Boost::hana ALIAS hana)` to the `CMakeLists.txt` would allow the advanced error reporting also internally and when used via `add_subdirectory(hana)`

---

## Comment 2

> Username: urp  
> Created at: 2018-07-09 21:48:41 UTC  
> Updated at: 2018-07-10 00:07:36 UTC  
> Url: https://github.com/boostorg/hana/issues/414#issuecomment-403631338  

Even more flexibility can be achieved by adding `export( PACKAGE hana )` which exports the build configuration to the cmake package registry.  
  
This is what I came up with in my project  which also relies on `CMake >= 3.9`:  
```  
  ### Create package config in build directory ###  
  
  set( build_config_location ${CMAKE_CURRENT_BINARY_DIR} )  
  
  export( EXPORT ${CONFIG_PACKAGE_NAME}-targets  
    FILE  
      "${build_config_location}/${CONFIG_PACKAGE_NAME}-targets.cmake"  
    NAMESPACE  
      ${CANONICAL_PACKAGE_NAMESPACE}::  
  )  
  
  write_basic_package_version_file(  
    "${build_config_location}/${CONFIG_PACKAGE_NAME}-config-version.cmake"  
    VERSION  
      ${CONFIG_PACKAGE_VERSION}  
    COMPATIBILITY  
      AnyNewerVersion  
  )  
  
  configure_package_config_file(  
    "${CANONICAL_MODULE_DIR}/CanonicalExport/package-config.cmake.in"  
    "${CONFIG_PACKAGE_NAME}-config.cmake"  
    INSTALL_DESTINATION  
      "${build_config_location}"  
  )  
  
  ### Export build directory into local cmake registry ###  
  
  export( PACKAGE ${CONFIG_PACKAGE_NAME} )  
  
  ### Install package config ###  
  
  set( install_config_location ${CMAKE_INSTALL_LIBDIR}/cmake/${CONFIG_PACKAGE_NAME} )  
  
  install( EXPORT ${CONFIG_PACKAGE_NAME}-targets  
    DESTINATION  
      ${install_config_location}  
    NAMESPACE  
      ${CANONICAL_PACKAGE_NAMESPACE}::  
    COMPONENT  
      ${CONFIG_PACKAGE_COMPONENT}  
  )  
  
  install(  
    FILES  
      "${build_config_location}/${CONFIG_PACKAGE_NAME}-config.cmake"  
      "${build_config_location}/${CONFIG_PACKAGE_NAME}-config-version.cmake"  
    DESTINATION  
      ${install_config_location}  
    COMPONENT  
      ${CONFIG_PACKAGE_COMPONENT}  
  )  
```  
with `package-config.cmake.in`:  
```  
@PACKAGE_INIT@  
  
# include(CMakeFindDependencyMacro)  
# find_dependency( ??? 0.1.0)  
  
include( "${CMAKE_CURRENT_LIST_DIR}/@CONFIG_PACKAGE_NAME@-targets.cmake" )  
  
include( "${CMAKE_CURRENT_LIST_DIR}/@CONFIG_PACKAGE_NAME@-macros.cmake" )  
```  
See ["Effective CMake" by Daniel Pfeifer](https://www.youtube.com/watch?v=bsXLMQ6WgIk) for details

---

## Comment 3

> Username: urp  
> Created at: 2018-08-03 16:30:24 UTC  
> Updated at: 2018-08-04 01:08:59 UTC  
> Url: https://github.com/boostorg/hana/issues/414#issuecomment-410307027  

Are you interested in a pull request implementing the proposal(s) above?

---

## Comment 4

> Username: ldionne  
> Created at: 2018-08-06 15:06:46 UTC  
> Url: https://github.com/boostorg/hana/issues/414#issuecomment-410740628  

Yes, I would be interested in reviewing a PR. I am aware (and a big fan of) Daniel Pfeifer's talk, but I haven't found the time to implement those changes in Hana myself.
