# #114 - Serialization-related tests on Windows are failing with gcc [Open]

> Username: chandryan  
> Created at: 2025-09-27 15:39:36 UTC  
> Updated at: 2025-09-27 15:39:36 UTC  
> Url: https://github.com/boostorg/msm/issues/114  

Serialization-related tests on Windows are currently failing with gcc, they have to be disabled for the moment. This ticket serves for tracking the disabled test cases and providing more context why the test cases are disabled.  
  
The disabled test cases actually pass, but eventually a segfault in a singleton destructor from Boost::serialization occurs before the application exits. The segfault seems to be related to a bug in gcc's implementation of `std::type_info::before()`.  
  
Oddly this bug only surfaces on Windows and depends on how the constant `__GXX_TYPEINFO_EQUALITY_INLINE` is defined in the toolchain. It seems to evaluate to 1 on Linux environments and 0 on Windows. There is a [bug report for gcc](https://gcc.gnu.org/bugzilla/show_bug.cgi?id=117405) describing this issue. I managed to get a successful run of the disabled test cases with the following hack to ensure that they are indeed related to this  gcc bug:  
- Add `#define __GXX_TYPEINFO_EQUALITY_INLINE 1` at the top of `serialization/src/extended_type_info_typeid.cpp`  
- Link serialization as shared lib instead of static
