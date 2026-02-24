# #61  Add configuration macro BOOST_PARAMETER_COMPOSE_MAX_ARITY [Merged]

> Username: CromwellEnage  
> Created at: 2019-01-14 22:06:06 UTC  
> Updated at: 2019-01-16 05:54:32 UTC  
> Merged at: 2019-01-16 04:08:23 UTC  
> Closed at: 2019-01-16 04:08:23 UTC  
> Url: https://github.com/boostorg/parameter/pull/61  

Decouple the compose() function and the BOOST_PARAMETER_NO_SPEC_* code generation macros from BOOST_PARAMETER_MAX_ARITY for older compilers so their generated functions can take in more arguments.

---
