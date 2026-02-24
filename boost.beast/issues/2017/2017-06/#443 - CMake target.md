# #443 - CMake target [Closed]

> Username: xsacha  
> Created at: 2017-06-08 16:51:58 UTC  
> Updated at: 2017-07-22 12:22:54 UTC  
> Closed at: 2017-07-22 12:22:54 UTC  
> Url: https://github.com/boostorg/beast/issues/443  

An interface target is missing. It is very easy to add, see code sample below.  
The includes and linkage (Boost) can be added to this.  
  
Adding this will greatly simplify your own CMakeLists. See: your usage of include paths and library linkage vs my commit.  
  
```  
### Library ###  
add_library(${PROJECT_NAME} INTERFACE)  
target_link_libraries(${PROJECT_NAME} INTERFACE Boost::coroutine Boost::context Boost::thread Boost::filesystem Boost::program_options Boost::system)  
  
### Install ###  
set(generated_dir "${CMAKE_CURRENT_BINARY_DIR}/generated")  
  
set(config_install_dir "lib/cmake/${PROJECT_NAME}")  
set(include_install_dir "include")  
  
set(version_config "${generated_dir}/${PROJECT_NAME}ConfigVersion.cmake")  
set(project_config "${generated_dir}/${PROJECT_NAME}Config.cmake")  
set(targets_export_name "${PROJECT_NAME}Targets")  
set(namespace "${PROJECT_NAME}::")  
  
include(CMakePackageConfigHelpers)  
write_basic_package_version_file(  
    "${version_config}" COMPATIBILITY SameMajorVersion  
)  
  
# Note: use 'targets_export_name'  
configure_file("cmake/Config.cmake.in" "${project_config}" @ONLY)  
  
install(  
    TARGETS ${PROJECT_NAME}  
    EXPORT "${targets_export_name}"  
    INCLUDES DESTINATION "${include_install_dir}"  
)  
  
install(  
    DIRECTORY "include/"  
    DESTINATION "${include_install_dir}"  
)  
  
install(  
    FILES "${project_config}" "${version_config}"  
    DESTINATION "${config_install_dir}"  
)  
  
install(  
    EXPORT "${targets_export_name}"  
    NAMESPACE "${namespace}"  
    DESTINATION "${config_install_dir}"  
)  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-06-21 03:57:09 UTC  
> Url: https://github.com/boostorg/beast/issues/443#issuecomment-309956149  

It looks like we have the interface target now? Is this issue still relevant?

---

## Comment 2

> Username: xsacha  
> Created at: 2017-06-21 05:40:54 UTC  
> Url: https://github.com/boostorg/beast/issues/443#issuecomment-309969080  

If installing target isn't an option, then yes this can be closed.

---

## Comment 3

> Username: xsacha  
> Created at: 2017-07-22 06:31:00 UTC  
> Updated at: 2017-07-22 07:14:45 UTC  
> Url: https://github.com/boostorg/beast/issues/443#issuecomment-317160149  

@vinniefalco   
Just a note that it looks like Boost is officially moving to CMake:  
http://boost.2283326.n4.nabble.com/CMake-Announcement-from-Boost-Steering-Committee-tt4696934.html  
I know you've already seen this but just making a note here that maybe the target will be relevant.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2017-07-22 12:22:45 UTC  
> Url: https://github.com/boostorg/beast/issues/443#issuecomment-317180193  

It is possible yes although I am sure I will have to change something
