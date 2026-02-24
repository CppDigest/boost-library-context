# #59  Add configuration macro BOOST_PARAMETER_COMPOSE_MAX_ARITY [Closed]

> Username: CromwellEnage  
> Created at: 2019-01-14 20:33:30 UTC  
> Updated at: 2019-01-14 21:59:22 UTC  
> Closed at: 2019-01-14 21:59:21 UTC  
> Url: https://github.com/boostorg/parameter/pull/59  

Decouple the compose() function and the BOOST_PARAMETER_NO_SPEC_* code generation macros from BOOST_PARAMETER_MAX_ARITY for older compilers so their generated functions can take in more arguments.

---
