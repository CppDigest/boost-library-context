# #56  Add configuration macro BOOST_PARAMETER_COMPOSE_MAX_ARITY, add missing literate test, add BOOST_CONSTEXPR qualifiers IAW reference documentation, update test suite [Closed]

> Username: CromwellEnage  
> Created at: 2019-01-14 19:08:17 UTC  
> Updated at: 2019-01-14 20:06:38 UTC  
> Closed at: 2019-01-14 20:03:50 UTC  
> Url: https://github.com/boostorg/parameter/pull/56  

* Decouple the compose() function and the BOOST_PARAMETER_NO_SPEC_* code generation macros from BOOST_PARAMETER_MAX_ARITY for older compilers so their generated functions can take in more arguments.  
* s/LIBS_PARAMETER_TEST_COMPILE_FAILURE_MSVC/LIBS_PARAMETER_TEST_COMPILE_FAILURE_VENDOR_SPECIFIC in case compilers other than MSVC need to be tested for failures.

---
