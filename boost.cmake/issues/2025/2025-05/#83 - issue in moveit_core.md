# #83 - issue in moveit_core [Open]

> Username: JayL16  
> Created at: 2025-05-23 08:48:02 UTC  
> Updated at: 2025-12-14 07:57:22 UTC  
> Url: https://github.com/boostorg/cmake/issues/83  

i try to colcon build in a container of the docker file but this happens in the moveit_core about the osqp which cannot be solved after few tries. the error is showed below. can anyone help me please?  
  
  
CMake Error at CMakeLists.txt:23 (find_package):  
  By not providing "Findosqp.cmake" in CMAKE_MODULE_PATH this project has  
  asked CMake to find a package configuration file provided by "osqp", but  
  CMake did not find one.  
  
  Could not find a package configuration file provided by "osqp" with any of  
  the following names:  
  
    osqpConfig.cmake  
    osqp-config.cmake  
  
  Add the installation prefix of "osqp" to CMAKE_PREFIX_PATH or set  
  "osqp_DIR" to a directory containing one of the above files.  If "osqp"  
  provides a separate development package or SDK, be sure it has been  
  installed.

---

## Comment 1

> Username: Flamefire  
> Created at: 2025-09-12 18:51:30 UTC  
> Url: https://github.com/boostorg/cmake/issues/83#issuecomment-3286477971  

This doesn't seem to be related to Boost. Neither colcon nor moveit_core nor osqp are part of Boost.  
  
DId you mean to report this elsewhere?

---

## Comment 2

> Username: nigels-com  
> Created at: 2025-12-14 07:57:21 UTC  
> Url: https://github.com/boostorg/cmake/issues/83#issuecomment-3650447162  

OSQP could be the [convex quadratic program solver](https://osqp.org/docs/solver/index.html).  
But I wasn't able to find any mention of it in the boost 1.90.0 release.  
I'd recommend closing this ticket.
