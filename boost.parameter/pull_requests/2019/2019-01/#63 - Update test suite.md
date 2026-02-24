# #63 Update test suite [Merged]

> Username: CromwellEnage  
> Created at: 2019-01-16 08:22:41 UTC  
> Updated at: 2019-01-16 15:42:24 UTC  
> Merged at: 2019-01-16 14:08:43 UTC  
> Closed at: 2019-01-16 14:08:44 UTC  
> Url: https://github.com/boostorg/parameter/pull/63  

* Add "test/literate/parameter-enabled-function-call-operators0.cpp" to test suite under alias 'parameter_literate_tests'.  
* Add compose() test routine to "test/compose.cpp"  
* Replace LIBS_PARAMETER_TEST_COMPILE_FAILURE_MSVC with LIBS_PARAMETER_TEST_COMPILE_FAILURE_VENDOR_SPECIFIC in case compilers other than MSVC start exhibiting errant behavior.  
* Adjust for the fact that BOOST_PARAMETER_MAX_ARITY is defined regardless of whether or not BOOST_PARAMETER_HAS_PERFECT_FORWARDING is defined.  
* Use minimal file headers to #include vice <boost/parameter.hpp>.

---
